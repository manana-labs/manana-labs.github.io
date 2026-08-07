---
title: "Four Design Decisions in Agentic Retrieval (Part 4): When the context comes to the model. "
date: 2026-07-22
author: Balaram, Shashank
---
[Previous post]({{ '/blogs/how-much-context-comes-back/' | relative_url }}) discussed about the amount of context that agent sees. In this, we'll discuss decisions related to when that context is seen by the agent. 


### When the context comes to the model. 
The last question is related to when a model can see the context. 

Eager fetching happens before the model starts. In Classic RAG, everything the model will see is decided upfront. User makes a query -> the most relevant content is fetched -> The model includes this into its context. This approach is more reproducible and easy to reason about. It is better when you already know what model needs to know about. 

In billing question discussed above, If we know with certainty that the information related to invoices, payment history and plan is required. The following can be done
```python
context = {
    "invoices": fetch_invoices(user.id, last_n_months=3),
    "plan": fetch_plan(user.id),
    "payments": fetch_payment_events(user.id, last_n_months=3),
}
answer = llm(question, context)
```
Here, only single llm call is required which is cheaper and faster.

---
Lazy fetching tries to overcome the limitation of above approach. What if model needs something more than what was passed as the context through RAG? Can model decide to fetch something else again? Here the model can perform fetch mid-reasoning. This is usually how the modern agents operate. 

Take an example of a coding agent. When user enters a command like,  "Add status column to Results table". The agent firstly lists the files hierarchy using a tool like `tree`, using its output, it decides it needs to see content of  `models.py`, then it fetches the current implementation of `Results` table, the migration status. It then writes new version. It never needs to read what information is there in irrelevant files. 
```python
> tree src/
> cat src/models.py          # found Results table
> cat src/migrations/0042.py # checks the latest migration format
> write src/migrations/0043_add_status.py
```

---

Another pattern, that we're using in one of our projects is a combination of both. Using bm25 + kNN search over a fixed user query, we eager load the documents and save it to  some temporary text file. The agent gets reference of that file. Now, agent can use lazy loading to retrieve what information it requires from this shortlisted content by using tools like `ripgrep`. 
```python
# Eager phase: narrow 100k docs to ~50
hits = hybrid_search(user_query, bm25_weight=0.4, knn_weight=0.6, k=50)
write_to_file("/tmp/shortlist.txt", format_docs(hits))

# Lazy phase: agent works over the shortlist only
agent.run(question, tools=[grep_file, read_lines], scope="/tmp/shortlist.txt")
```

It avoids agent from having to search from a huge storage of elasticsearch knowledge base. The iterative agent search with reasoning is also more accurate but also comes with a higher cost.  This resource talks about the similar setup. [HotelQuEST, Hadad et al., 2026](https://arxiv.org/abs/2602.23949) 

## Ending Notes
These four decisions are independent. To make that concrete, here is an example of where the hybrid pipeline explained in earlier sections sits:

**What gets asked**: Scoped. The agent phrases its own ripgrep patterns, but the reachable surface is one temp file. It cannot touch the Elasticsearch cluster.

**In what order the questions are asked**: ReAct. The agent greps, reads content, and decides its next pattern from what it found. No upfront plan.

**How much context comes back**: Filtered twice. The eager phase applies top-k over hybrid scores. The lazy phase returns only matching lines instead of whole documents.

**When the context comes to the model**: Both. Eager for the shortlisting over the full corpus, lazy for everything after.

Notice the dials don't move together. The sequence is fully agentic while the query surface is tightly capped. If "how agentic is your retrieval" were one question, this system would have no answer. Its four questions, and this system answers each one differently.