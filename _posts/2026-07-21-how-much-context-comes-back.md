---
title: "Four Design Decisions in Agentic Retrieval (Part 3): How much context comes back"
date: 2026-07-21
author: Balaram, Shashank
---
Our [previous post]({{ '/blogs/what-order-questions-are-asked/' | relative_url }}) on this four part series discussed about, how order of questions asked during information retrieval matter. In this part we'll discuss the ways to control how much context goes to the agent and why it matters. 

---
## How much context comes back
In agentic retrieval, every token added to the model's context contributes to the cost and latency. To keep the context concise you want to be selective of how much of retrieved content gets fed to the model. 

For eg, if you fetch 50 documents, not all 50 documents have to go to the model's context. 
The standard tools you have access to, to control how much context comes to the model are: 


**Top-K with a threshold**: Say only top 5 documents, that outscore a given similarity threshold will be part of the context. If only 10 documents pass the threshold score, 5 are selected, if only 3 documents do so, only 3 are selected and rest are rejected. Re-ranking is quite common here. You fetch 10, use dedicated re-ranker to shuffle their actual closeness with the query and then select the top-5 only. 

For example, a search over a company's help center with 10,000 articles: 
```python
candidates = vector_search("duplicate charge refund", k=50)

reranked = reranker.rerank(query, candidates)

context = [d for d in reranked[:5] if d.score > 0.72]
```



**Metadata**: A filter can be applied on the property of data being fetched.

For example, the articles are in 5 different languages, and user is an English speaker. In that case: 
```python 
candidates = search(query, filter={"lang": "english"})
```
without the filter, the top results might be in German, which are semantically close but useless to the user.



**Permission filters**: The filter may require access control. It's not enough to enforce access control via prompts. The retrieval system must be designed in an appropriate way.

For example, the articles can contain some internal-only articles which are related to fraud investigation procedures. When an internal staff is requesting, the articles should retrieve those internal-only articles too. Whereas when an outside user is requesting, those articles should be excluded. 



**Summarization**: In multi-agentic systems, one agent may need to take care of  high volume of context. In those cases, it might not be appropriate to provide raw retrieved content into the model's context. In those cases a separate llm call is made, for summarizing all retrieved content. Which is then passed into the main model's context. It helps prevent bloating the main agent's context. It is also a popular practise with sub-agents. The sub-agents perform a series of steps to figure out some information. The main agent only sees the summarized output from the sub-agent which is required for it to proceed ahead. 

For Example: To investigate the double-charge incident, a sub-agent may be asked to investigate and read multiple payment gateway log entries, incident tickets, docs etc. The main agent requires none of those. The sub agent could come with summary like the following which is enough for main agent:  
```text
- Incident INC-444 (June 10) caused gateway timeouts which resulted in duplicate transactions. 
- Auto-refund processed for 100 accounts. 
- This user's refund not in the processed batch and hence flagged for manual review
```


In [the next part]({{ '/blogs/when-the-context-comes-to-the-model/' | relative_url }}), we'll discuss lazy and eager context fetching. 