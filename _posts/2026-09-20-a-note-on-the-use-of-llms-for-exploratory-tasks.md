---
title: "A Note on the Use of LLMs for Exploratory Tasks"
date: 2026-09-20
author: Shashank Srikant
---

(This note was presented to my team in an internal talk. It refers to a working example from an internal project the team was involved in.)

I wanted to write you a note on the use of AI when doing any kind of exploratory analysis. This is a note specifically targeted to people starting their careers.

Our recent conversations should have highlighted the limitations of working with AI. These conversations highlight a problem that has plagued the software engineering and programming languages community for several decades now: the limitation has never been in our ability to crank out code. It's always been in specifying what we need the code to do.

Being able to generate code for a given specification is mostly a solved problem today with AI. That's what AI is good at. For a tightly defined spec (more on this below), it is good at generating what's statistically the most common way to solve the problem. Imagine writing a captcha solver, where the inputs, outputs, and functionality are mostly well defined. This problem ought to have been solved by the community a while ago, and we're invoking that communal knowledge that's baked into a statistical predictor (AI model) to predict the sequence of characters (code) that will solve the problem.

The real challenge appears when we don't know what we want. Such cases routinely appear before us: exploring data that we haven't seen before, looking for knowledge/facts/citations/resources that may not even exist out there, writing code for a problem we don't fully understand or appreciate, looking for medical knowledge we don't understand, and so on.

What should be our mental model, our approach in such cases? The pursuit of science and the tools of reasoning developed over the last several centuries should provide some guidance.

There is significant literature that suggests a common limitation in human reasoning: when developing a hypothesis (a mental model of a phenomenon: for example, how are states represented in the Drupal data dump we're analyzing), we as a species tend to exhibit two harmful behaviors:

1. **I believe a hypothesis H. I then find observations consistent with H**: here you go into the problem with a biased model H, and seek data and observations that conveniently only explain H.
2. **I am presented with a hypothesis H. I accept it and form other hypotheses based on H**: it's a variant of #1 but different. You do not challenge a hypothesis presented to you, and accept it at face value.

Modern science has progressed by challenging just these biases. When presented with H, scientific reasoning often starts by asking what evidence could falsify H, rather than looking for evidence that confirms H. And in the search to falsify H, you will gather information about H.

A simple example:

Your Wi-Fi technician tells you that the Wi-Fi is slow because too many devices are connected. To falsify this claim, you disconnect every other device, connect only one device, and test your speed.

- If the speed remains bad, the technician is giving you bs. You have falsified their hypothesis.
- If the speed gets better, there could still be alternate explanations for the improvement. But there is merit to what the technician claims.

Note: This is related to the intuition behind null hypothesis testing.

Here's some foundational relevant literature from more than 40 years ago:

- Skim through the works of Wason to learn more about the clever experiments he set up to establish confirmation bias. [Link](https://en.wikipedia.org/wiki/Peter_Cathcart_Wason)
- Read Klayman and Ha's work from 1987 on misinformation and how we acquire knowledge. [Link](http://stats.org.uk/statistical-inference/KlaymanHa1987.pdf)

This may all seem obvious to all of you. But here's the catch: very few of us truly imbibe this mindset while working with an LLM (or in other aspects of life, in general). A language model is a hypothesis explorer. It's statistically picking the most likely hypothesis for the problem you are specifying, generating code and results accordingly.

But pay attention to the communication game being played here:

- the true hypothesis is H
- your understanding of H is an incomplete representation of it: H<sub>you</sub>
- you prompt the LLM, and the LLM forms its own interpretation of what you mean: H<sub>llm</sub>
- the code C it produces is conditioned on H<sub>llm</sub>, not directly on H

The generated code likely pertains faithfully to H<sub>llm</sub>: no issues there. That's what LLMs have been trained well to do, and that's what they are actually good at. But notice: H<sub>you</sub> and H<sub>llm</sub> are latent: they are not specified in any concrete way; they are not written down, and you do not have a concrete sense for them. You are just observing the artifact C and implicitly judging the goodness of H<sub>llm</sub>, H<sub>you</sub>, and H.

This is where the problem arises: you are blind to how far H<sub>llm</sub> is from H<sub>you</sub>, and importantly, how far H<sub>you</sub> is from H. And worse, the artifact C "looks" correct, it's formatted nicely, it has some numbers that seem right at first glance, and has explanations that look right. It likely has references to citations that may be legit, and may even have your favorite emoji. But does C faithfully represent H? Very likely no.

This is where the real game is. And this is where I want you folks to seriously exercise your muscle of invalidating working hypotheses.

A code or results artifact produced by an LLM should be treated as incorrect by default, unless you can validate it by writing tests, cross-referencing it with other data, or manually checking whether the calculations hold up. I will assume (and you should too) that any result you share that an LLM has produced but which you cannot explain is false. The onus then is on you to prove me wrong. And in the process, prove yourself wrong.

Please invest in this muscle. LLMs cannot substitute for developing the ability to understand and validate unfamiliar problems. I don't want to be impressed by how quickly you can finish a task. I will be very impressed by how deeply you understand the nuts and bolts of your task. Sure, AI will help reduce the time it takes to implement things we understand well drastically. But in any exploratory or research work, there are things we don't understand. In such cases, depth typically outperforms pace. I'd strongly recommend training yourself to explore and validate bite-sized problems first. This may take a few months. Be patient. Once you're comfortable, most certainly move on to the autopilot/assisted modes that LLMs provide.

When you get an LLM to generate a result for a task whose details you do not understand, pause, ask yourself what information can falsify the results. It's going to take time, it's ok, and please invest that time. It will only help build that muscle in you, and you will see yourself getting better and quicker at it.

The point isn't to avoid using LLMs. Quite the opposite. Use them aggressively when you understand the problem. But when you're exploring something unfamiliar, don't outsource the exploration itself. Build the habit of asking what you believe, what you don't know, and what evidence would prove you wrong. That's the muscle that will compound over the course of your career.
