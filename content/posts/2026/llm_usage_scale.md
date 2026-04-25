+++
title = 'A Kardashev Scale for Working With LLMs'
date = '2026-04-07'
draft = false
tags = ['dev']
#author = 'adriano'
header_image = "/images/llm_scale.png"
+++

The arrival of ChatGPT at the end of 2022 now feels strangely distant. In just a few years, interacting with large language models has gone from novelty to routine. Sometimes that interaction is explicit. We open ChatGPT, Perplexity, Claude, Gemini, or Grok and ask for help. More often it is embedded in the products we already use, because nearly every software team now feels pressure to ship something labeled _"AI"_.

Most people still talk about this shift in terms of models: which one is smarter, which one has a larger context window, which one benchmarks better. That matters, but it misses the more useful distinction. I see LLMs as black boxes trained on massive amounts of data, and we interact with them through a window, the context window, whether that is **200k**, **1M**, or something else. What changes the result the most is not the black box itself, it is the interaction and the context we give to that window.

That is why it helps to think less about model prestige and more about delegation. A useful framing device is the Kardashev Scale, a concept proposed by astronomer Nikolai Kardashev that classifies civilizations by the amount of energy they can harness and control. In its original form, **Type I** means control at the **scale of a planet**, **Type II** at the **scale of a star**, and **Type III** at the **scale of a galaxy**. The astrophysics is not the point. What matters here is the structure: a progression in control, scope, and operational power.

Applied to LLMs, that structure maps surprisingly well to how people actually use them. **Type I** is the conversational assistant. **Type II** is the tool-using coding operator. **Type III** is the autonomous software agent. The move between levels is not about prompt cleverness. It is about how much real work is being delegated and how much orchestration still depends on a human.

## Type I: Conversational Assistant
This is the default mode most people start with. You open a chat, ask a question, get an answer, and then you go do the actual work yourself. The model helps you think, explain, outline, summarize, compare options, or unblock a problem. It is useful, but the output is still mostly text, and the execution layer is still you.

For a developer, Type I looks like asking for help designing a service boundary, debugging a stack trace, comparing Kafka with RabbitMQ, or expanding an article outline. It is fast and cheap, which is why it became habit forming so quickly. The friction is low, and the feedback loop is immediate.

The limit is also obvious. The model is not acting in your environment. It is not touching your codebase, not running tests, not opening files, not checking whether its own answer actually works. Type I gives you assisted thinking, not assisted execution.

That is why a lot of _"I use AI every day"_ claims are overstated. Many people are just using a better search and draft tool. That is not trivial, but it is also not the same thing as delegating meaningful work.

## Type II: Vibe Coder
Type II starts when the model moves out of the chat box and into your machine. Tools like **Claude Code, Codex, Cursor, Gemini CLI**, and similar systems change the interaction model completely. Now the LLM is not just answering. It is reading files, editing code, running commands, writing tests, and implementing things.

This is where the productivity jump becomes real. You stop asking, _"How would I build this?"_ and start saying, _"Implement this endpoint, add tests, refactor this module, and update the docs."_ The model becomes an operator inside your development environment.

But this is also where people get sloppy. Vibe coding works well right up until the point where the repo gets larger, the task gets less local, or the chat gets polluted with too much history. Then the model starts losing the plot. It edits the wrong abstraction, duplicates code that already exists, forgets constraints mentioned twenty minutes earlier, or confidently breaks things while sounding organized.

The biggest practical problem here is **context rot**. The longer the session, the worse the signal to noise ratio gets. People blame the model, but often the workflow is the problem. They are trying to push planning, execution, review, and correction through one giant, bloated conversation.

## The Bridge: Human-Orchestrated Agents
After enough vibe coding, I started changing the way I worked. Not because the tools were bad, but because the interaction pattern was inefficient.

The process became more structured. First, I discuss the idea in a regular chat tool like Perplexity, ChatGPT, or Grok. I go back and forth until the idea is clearer, the trade offs are explicit, and the actual task is better defined.

Then I ask that chat to produce an execution prompt and break the work into tasks. At that point, I move into the coding tool. Instead of throwing the full project at it in one session, I run the tasks sequentially.

For each task, I clear the context and start fresh. That matters more than most people admit. Resetting the context avoids dragging stale assumptions, dead branches of reasoning, and irrelevant chatter into the next implementation step.

After each task, I check the result. If it looks correct, I move to the next step. When the implementation is done, I ask for a deeper review: code quality, edge cases, security concerns, maybe performance if the feature touches a hot path.

That is already agentic orchestration, just with a human in the loop. I am acting as the planner, the memory manager, the task router, and the approval gate. The model is doing meaningful execution, but I am still controlling the sequence and cleaning up its operating conditions.

This matters because it exposes a useful truth: better outcomes do not always come from a better model. A lot of the gain comes from a better operating model. The difference between mediocre AI assisted work and strong AI assisted work is often task decomposition, context hygiene, and review discipline.

## Type III: Autonomous Agents
Type III starts when the orchestration stops depending on manual prompting between each step. The system itself handles planning, decomposition, execution, verification, and escalation. The human sets the objective, constraints, and approval gates, but does not micromanage every transition.

In practical terms, that means an agent or multi-agent workflow can take a goal, break it into tasks, assign those tasks, manage context per step, run tools, execute tests, review its own output, and only come back when it hits uncertainty, a permission boundary, or a failed check. The human is still responsible, but no longer acts as the real time task scheduler.

A concrete example is a feature request flowing from issue to pull request with minimal manual intervention. One part of the system refines the requirement. Another turns it into an implementation plan. Another works on the code. Another runs tests and static analysis. Another reviews for security or obvious regressions. If the confidence is high, the system opens a PR with evidence attached. If confidence drops, it escalates.

That is very different from "I asked Claude Code to write a feature." One is automation with orchestration. The other is assisted coding with branding.

Most teams are not at Type III yet, even if they say they are. They are doing Type II with better tooling, or Type II plus a human who manually sequences the work. That is still useful, but it is not autonomy. If the system needs you to decide every next step, you do not have an autonomous agent. You have a fast intern with terminal access.

The main barrier is not model intelligence alone. It is engineering. Type III needs bounded tasks, tool permissions, verification loops, rollback paths, logs, and clear failure handling. Without that, “agentic” is just a marketing label for unreliable automation.

The real upgrade is not using AI more often. It is moving from one off prompting to repeatable delegation. Once you see that, the hype becomes easier to filter. Bigger context windows and smarter models help, but they do not fix a bad interaction model.

If you want better results right now, stop stuffing everything into one long chat and hoping the model stays sharp. Break work into explicit tasks, reset context aggressively, verify each step, and treat autonomy as a systems design problem. That is how you move up the scale.