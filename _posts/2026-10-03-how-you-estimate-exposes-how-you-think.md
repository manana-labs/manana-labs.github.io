---
title: "How You Estimate Exposes How You Think"
date: 2026-10-03
author: Balaram
---

Estimation problems don't have a single right answer. And this inherent property is why they reveal the way someone thinks. 

In this article, I will try to explain my reasoning through these points: 
1. **Recursive Decomposition**: Why estimation requires decomposition, often recursively until you reach something you can reason about. 
2. **First Principles Thinking and Cues from the Known**: Why estimation requires deriving an answer from simpler reasoning, then using what you already know to choose reasonable values.
3. **Which Assumptions Matter?**: Why a good estimate depends on identifying which assumptions can significantly affect the final result
4. **Sanity Checking**: How to test whether the estimate is at least plausible before trusting it.

Estimation problems keep arising in different shapes and forms in my regular day-to-day work. Often, there’s no clarity around the problem itself, and the information is not enough.

Before picking an infrastructure configuration, I may estimate the expected traffic, per-process usage of RAM/CPU/storage, and how this scales with each user. Before committing to an idea, I try to estimate whether the bottleneck I am worried about is even significant enough to matter.

The same thing happens outside work. If I am planning a trip, I might roughly estimate how much time and money it will take before deciding whether it is even practical.

And trying to find answers to such questions made me curious how we usually arrive at an answer.

### Example
Let's start with on example estimation question: 
*How many cups of coffee are sold in Kathmandu in a day?*

I don't know the answer, and there isn't a census or any official data for it either. But we can still try to get closer to a reasonable answer. 

My initial chain of thoughts would be: 
Find the population of Kathmandu -> estimate what fraction of people drink coffee in each age group -> estimate how many cups of coffee one person drinks each day -> estimate what fraction of those cups are actually bought from cafes or shops.

so roughly: 
`population × fraction who drink coffee × cups per person × fraction bought outside`

I still don’t know most of these numbers exactly. But the original problem is now broken into smaller problems that are easier to reason about. For now, let's say Kathmandu has around 1.5 million people.

And that is where the interesting part starts.
### Recursive Decomposition

The first thing I did in the example above was break one large unknown into smaller unknowns.

But those smaller problems may still not be directly answerable. I may not know what fraction of people in each age group drink coffee. So I may have to break that down further, or find another way to approximate it from something I already know.

For example, instead of directly estimating what fraction of people in Kathmandu drinks coffee, I could split the population into broad age groups and estimate coffee consumption for each group separately. Even one of those groups may still be too broad, so I may break it down again based on students, office workers, and others. Let's say, after doing this, I estimate that around 25% of the population drinks coffee. That gives us roughly 375,000 coffee drinkers.

This process can continue recursively until I reach a point where the remaining sub-problems are simple enough to estimate, or already have a definitive answer to them.

### First Principles and Cues from the Known

Once the problem is broken down enough, there is another issue: you still may not know the answers to the smaller parts. This is where first-principles thinking becomes useful. Instead of trying to recall a number, you try to derive it from things you understand. 

Say I want to estimate how many cups of coffee an office worker drinks in a day. I may not know the average, but I can think through a typical day: maybe one cup in the morning, and for some people, another after lunch. So, let's use 1.5 cups per person per day as a rough estimate. At the same time, I can use cues from what I already know to estimate how many of those cups are actually bought from cafes or shops. Let's say around one-third are bought outside. None of these cues give me the answer directly, but together they help me avoid a completely blind guess.

That gives us:

`375,000 × 1.5 × 1/3 ≈ 187,500 cups`

So, our rough estimate is around 190,000 cups of coffee sold in Kathmandu in a day.

### Which Assumptions Matter?

Not every assumption is worth the same amount of attention.

In the coffee example, getting the population of Kathmandu slightly wrong may not change the estimate much. But getting the fraction of people who actually drink coffee, or the fraction of cups bought outside, wrong could shift our 190,000 estimate a lot.

So part of estimation is judging which sub-problems matter most to the final answer.

If one assumption can change the result by 2x or 5x, it deserves more thought. If another only changes it by a few percent, a rough approximation is probably enough.

### Sanity Checking

Once I have an estimate, I usually try to poke holes in it before I trust it.

The first check is simple: does the number even feel plausible?

Our estimate of around 190,000 cups means roughly one bought coffee for every eight people in Kathmandu each day. That does not immediately sound absurd. If the estimate had instead come out to ten million cups, that would immediately raise questions.

I also like changing the important assumptions a little and seeing what happens. If a small change in one assumption completely changes the final answer, then I know the estimate is fragile and I should be more careful with it.

And when possible, I try to estimate the same thing from another direction. Instead of starting from population, I could start from the number of cafes. If I assume around 1,500 places sell coffee and each sells roughly 120 cups a day, that gives me 180,000 cups. That is fairly close to our first estimate of 190,000, so both approaches at least land in the same ballpark.

The practical takeaway for me is simple: before using an estimate to make a decision, try to break it once. If it still holds up reasonably well, it is probably useful enough.

### Conclusion

Looking back at the coffee example, whether the real number is 150,000, 190,000, or 250,000 cups is probably the least interesting part.

What matters more is how the problem was broken down, which assumptions were treated as important, what reference points were used, and whether the result was challenged.

That is why estimation is such a useful way to understand how someone thinks. The answer may be rough, but the way someone gets there can reveal a lot.

