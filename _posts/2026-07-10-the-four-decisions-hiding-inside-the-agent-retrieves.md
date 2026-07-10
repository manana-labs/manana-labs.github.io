---
title: "Agent Retrieval Design Decisions: When to Use Which Approach"
date: 2026-07-10
author: Balaram Neupane
---

A few weeks ago I wrote about tool types, and how sorting an agent's tools by what they can reach tells you a lot about the system before you read a single line of code. That post covered what an agent can touch. It didn't explain anything about how information travels from those tools into the model's context window. 

This post is about that second part, In this post, I try to explain some of the control levers you can tweak when talking about retrieval. 

Here's a quick way to see the problem. Imagine a design review where someone describes their system as "the agent retrieves relevant documents and answers the question." Everyone agrees. Now ask three people in that room to sketch what they just heard. One will draw a fixed query template hitting a vector store before the model runs. Another will draw the model writing its own search calls in a loop, deciding on its own when to stop. The third will draw something in between. All three sketches fit the sentence perfectly, and they are three very different systems.

"Retrieval" compresses at least four separate decisions, and each one can be made independently of the others. When I read an agent architecture now, these are the four questions I ask. Together they form something like a control plane for information fetching. The data plane is the retriever itself, the embeddings, the index. The control plane is everything that decides what gets asked, in what order, how much comes back, and when.

## Who writes the query

The first question is about authorship. Who actually composes the search or retrieval call?

At one end, the query is fixed. It's written ahead of time, maybe templated eg with a user ID or a date, and it never changes based on what the model is thinking. We can test it in isolation. We know exactly what it will do Today because it did the same thing yesterday.

At the other end, the model writes its own queries. It picks the tool, phrases the search, looks at the results, and decides whether to go again. If you've watched a coding agent ripgrep its way through a repository, refining its pattern each time, you've seen this. It's powerful and it's the least predictable option on the list.

There's a third position that I think deserves more attention: scoped. The model writes whatever query it wants, but the reachable surface underneath is limited. The database credential only sees one table. The file search runs inside a sandboxed directory. The model has full expressive freedom above a hard floor it cannot dig through.

The scoped is important because it's the one that still holds when things go wrong. A fixed query is safe but rigid. A free query is flexible but depends entirely on the model behaving. A scoped query stays safe even when the model is confused, or when someone has stuffed adversarial instructions into a document it just read. A model isn't designed only for good days. It should hold itself together even when retrieved document contains malicious instructions. 

## Who decides the sequence

The second question is separate from the first, and keeping them separate took me a while.

Query authorship is about a single call. Sequence is about the shape of the whole operation. The options run roughly like this:

A fixed DAG wires the steps in advance. Fetch, then filter, then generate, always in that order. The model can still own the query inside each step, but it can't reorder the steps or add new ones.

Plan-then-execute lets the model draft the sequence first, then run it. You get one moment where the whole plan is visible and can be checked before anything happens.

Free ReAct hands the model the controller on every turn. Look at the state, pick the next action, repeat until done.

Reach goes up as you move down that list, and so does the number of ways the run can fail. That part is unsurprising. What's more useful is noticing that this axis and the previous one don't move together. You can put a tightly scoped, single-table query inside a completely free ReAct loop. You can also give a rigid three-step DAG full access to your entire data source. These are very different risk profiles, and if you only ask "is this agentic or not," you can't tell them apart.

I went back and forth on whether these are genuinely two axes or one axis I was slicing too finely. What convinced me is that the failure modes differ. A bad query fetches the wrong thing once. A bad sequence compounds, because every step feeds the next one.

## How much actually lands in context

Fetching fifty documents is not the same as injecting fifty documents into the prompt. This is the dimension people skip most often, probably because the fetch feels like the event and the injection feels like plumbing.

The standard tools here are familiar. Top-k with a score threshold, so weak matches never make it in. Metadata and permission filters applied at query time. A re-ranking pass before you take the top slice. Summarization, so the model sees a compressed account of a document rather than the document itself.

One detail worth calling out: the permission filter does double duty. It cuts volume, which saves tokens and keeps the context clean, and it enforces access at the same moment. A document the user isn't allowed to see never even competes for a context slot. When one mechanism handles both cost and access, that's usually a sign it's sitting in the right place in the pipeline.

## When, and how often

The last question is timing relative to generation.

Eager fetching happens before the model starts. Classic RAG. Everything the model will know is decided up front, which makes runs reproducible and easy to reason about, and also means you're guessing at what will be needed.

Lazy fetching happens mid-reasoning, when the model hits a gap and reaches for a tool. Iterative fetching goes further: fetch, reason, fetch again, hop across documents. Multi-hop buys you reach, and pays for it in tokens and in a larger surface for things to go sideways, because now the results of fetch one shape the query of fetch two.

There's a fourth pattern we've been using in one of the agentic systems: return a reference instead of the payload. A cheap BM25 pass finds candidate documents, but instead of pushing their contents into the prompt, the pipeline hands the model a reference to them. The model then runs its own ripgrep-style search inside that referenced set when it actually needs something. The load defers until the model pulls on it. In practice most references never get pulled, and the context stays small without anyone having to predict up front what the model would need.

## Reading a system through these four questions

Who writes the query. Who decides the sequence. How much lands in context. When the fetch happens. Four knobs, mostly independent, and every retrieval setup is a position on all four at once.

The reason I find this framing useful is that safety and cost live in these knobs, and almost nowhere else. Teams pour effort into embedding models and chunking strategies, which is data plane work, and it's real work. But when an agent leaks something it shouldn't have, or burns ten times the expected tokens, or does something baffling on step six, the postmortem almost always lands on one of these four questions. Usually one that nobody remembered deciding, because it was decided implicitly, by whatever the framework did by default.

Next time someone tells you their agent "retrieves relevant documents," ask them the four questions. The answers are the architecture.
