---
title: "Four Design Decisions in Agentic Retrieval: Part 1 Introduction"
date: 2026-07-19
author: Balaram, Shashank
---
A previous post discussed tool types [Link](https://mananalabs.ai/blogs/three-ways-agents-use-tools-and-when-to-pick-each/), which explains how we can bucket tool  usage into different types based on how they're wired to the agent. It didn't discuss how information from those tools reach the context window of the model. 

In this post, we'll discuss different retriever related decisions which affect the way you design AI-first retrieval systems. In these systems, people spend a lot of time worrying about data organization: chunks size, embedding quality, etc. but we think there are "retriever" related actions which are equally important. 

Since this blog contains dense content, we're breaking it down into 4 different posts. The link to next part will be at the end of this post.

Information retrieval in agents spans a spectrum. At one end are fixed retrieval pipelines. Here, the retrieval process is predefined. For example, the system embeds the user's query, retrieves the top k documents from a vector database, and passes them to the model. The model has no control over what gets retrieved or how many retrieval steps are performed. 

At the other end are autonomous agents. They decide what information to retrieve, formulate their own search queries, and make multiple retrieval calls if needed. They may search different data sources, refine their queries based on intermediate results, and stop only when they determine they have enough context to answer the question. 

<img src="../images/agentic-retrieval.png">

Most practical systems lie somewhere between these two ends, combining fixed retrieval with varying degrees of model-driven decision making. Although they are often grouped under the same label, these systems differ substantially in architecture, capabilities, and trade-offs. This blog discusses such systems.


## The substeps involved in retrieval
Beyond how data is organized (eg. as full text, chunked text, embeddings etc), retrieving information from such data involves four separate decisions which are often overlooked. The decisions are: What gets asked, in what order, how much comes back and when. Think of them as four independent dials, each with its own settings.

### What gets asked 

At one end, the query is fixed. It's written ahead of time, maybe templated eg. with a user ID or a date, and it never changes based on what the model is thinking. We can test it in isolation. We know exactly what it will do today because it did the same thing yesterday. <br>
The query below will always fetch the invoices. It could be for a different `user_id` or for a different `start_date`. 

```python
query_sql(
    """
    SELECT *
    FROM invoices
    WHERE user_id = {user_id}
      AND created_at >= {start_date}
    """,
    user_id=user.id,
    start_date=start_date,
)
```
---
At the other end, the model writes its own queries. It picks the tool, phrases the search, looks at the results, and decides whether to go again. If you've watched a coding agent `ripgrep` its way through a repository, refining its pattern each time, you've seen this. It's powerful and it's the least predictable option on the list.

Eg: 
```python
query_sql(
    sql="{llm_generated_sql}",
)
```
---
There's a third position which is more important. Scoped. The model writes any query it wants, but the reachable surface underneath is capped. Eg, agent can read only one table in the database, the file search can run in a sandboxed directory. This is important as the scoped is the mode that still holds when things go wrong. A fixed query is safe yet rigid. A free query is flexible but unsafe. A scoped query stays safe even when model is confused or when a malicious instruction is stuffed into the document. 

Eg: Here the llm's query can only fetch invoices from a fixed tenant database. It can't access any other tenant's database.  
```python 
query_sql(
    sql="{llm_generated_sql}",
    database="{tenant_database}",   # credential only has access to one tenant
    schema="invoices",              # or a restricted schema
)
```

In [the next post]({{ '/blogs/what-order-questions-are-asked/' | relative_url }}), we'll discuss about another decision, which is the order in which questions are asked. 