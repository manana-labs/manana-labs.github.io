---
title: "Four Design Decisions in Agentic Retrieval (Part 2): In what order the questions are asked"
date: 2026-07-20
author: Balaram, Shashank
---
The previous [Post]({{ '/blogs/four-design-decisions-in-agentic-retrieval/' | relative_url }}), discussed about how we can control what gets asked to the agent. In this we'll discuss the 

## **Order in which the questions are asked**


The second decision is the sequence of the questions being asked. The first one is about query authorship, and this decision is about the shape of the whole operation. 

One sequence is a fixed Directed Acyclic Graph(DAG) where the steps are pre-defined. The model can still own the query inside each step. But it can't re-order the steps or add a new one. <br>

For example: A support agent answering "Why was I charged twice this month?" could follow following DAG:  
```python
-> fetch_invoices(user_id, month)
-> fetch_payment_events(invoice_ids)
-> generate_answer(context)
```
Every billing related question will always follow this path. If the answer lived in a refund policy document which is not a part of the DAG, then this pipeline fails to provide an answer. 

---

Another sequence is a plan-then-execute sequence. Unlike the first approach, where a series of steps is fixed for each execution, the plan is created by an agent based on the retrieval required. 

Here a planner will generate the series of steps and an executor will perform the actual task. For the same above example, "Why was I charged twice this month?", this will work in following way  : 
```python
plan = planner_llm(question)
# TODO: remove below comment and describe plan. 
# [
#   {"tool": "fetch_invoices",       "args": {"user_id": 42, "month": "2026-06"}},
#   {"tool": "fetch_payment_events", "args": {"invoice_ids": "$step_1.ids"}},
#   {"tool": "search_docs",          "args": {"query": "duplicate charge policy"}},
# ]

for step in plan: 
    results[step.id] = run(step)
```
For a given execution, say the generated plan is: 
- Fetch this month's invoices of the user.
- Fetch payment events.
- Search docs related to duplicate charge policy.

Notice how the planner produced similar actions to the previous DAG, but has an additional action of `Search docs`. For a different question like `Can I get a VAT invoice?`, the planner could come with only 2 actions. This approach is adaptable. One can also setup the system to require human approval in between the planner and execution steps.  

---
A third type is the ReAct. Instead of fixing a plan and then executing, the agent can decide the next step to take after every action. The agent repeats it until done. There is no plan upfront, everything is decided on the fly.

For example, take a look at the decision trace of an agent below for the same query, `Why was I charged twice this month?`: 
```python
Thought: I need to see the charges first.
Action:  fetch_invoices(user_id=42, month="2026-06")
Observation: Two invoices, both $29, same day.

Thought: Same amount and date. Could be a payment retry. Check gateway logs.
Action:  fetch_payment_events(invoice_ids=[881, 882])
Observation: Invoice 882 was a retry after a timeout on 881. Both settled.

Thought: Both settled means a genuine double charge. Was a refund already issued?
Action:  fetch_refunds(user_id=42)
Observation: No refunds found.

Answer: You were double charged due to a payment retry. No refund has
been issued yet, here's how to request one...

```
Notice how this approach was intelligent enough to check for refunds. For first and second methods, this step had to be a part of the DAG or the plan respectively.

---

This decision related to "Order of questions" don't go hand in hand with the first decision of "What get's asked". They both provide you the control at two different levels of granularity. You can have a ReAct loop, while still using a fixed query in each step of the sequence. The tool `fetch_invoices` in above examples can be used with all 3 different methods. If there are multiple tools each with fixed queries, agent will keep iterating to find context that is relevant to answer the question. 


In [the next post]({{ '/blogs/how-much-context-comes-back/' | relative_url }}), we'll discuss how we can control the amount of context an agent sees.