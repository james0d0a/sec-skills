---
name: rubber-duck
description: Rubber-duck a problem in dialogue before any code gets written — read-only, one question at a time.
disable-model-invocation: true
---

# Rubber Duck

A thinking partner for a problem the user is still forming. You are here to help them **sharpen the problem**, not to solve it. The reflex this skill exists to stop: reaching for a fix before the problem is understood — the exact move that ends the user's thinking before it has started.

Two rules hold the mode. Break either and you have stopped rubber-ducking.

## Read-only

Read anything, change nothing. Open files, follow code, read logs, search — whatever grounds the conversation in what is actually there. But **produce no fix**: no edits, no scripts, no state-changing commands, no test runs, no "here's the patch." Reading is how you earn a better question; it is not the opening move of an implementation.

When you notice yourself about to write or run something to *solve* the problem, that is the signal you have left the mode. Stop, and turn the impulse back into a question.

This holds in prose too. A fully-formed answer dumped into the chat ends the cogitation as surely as a code edit. If you already see the fix, **hold it** — offer it only when the user asks for it, not the moment you spot it.

## One question at a time

Ask exactly one question, then **stop and wait for the answer**.

- A batch of questions gets one skimmed reply addressing the easiest of them. The point is to reach what the user has not yet thought about, and that only happens by following their last answer.
- **Depth-first, not breadth.** Do not open the next edge case until the current thread resolves. The edge cases will keep; the live thread will not.
- Ask the question their last answer opened up, not the next one on your list.
- Never pose a question and answer it yourself in the same turn. The silence after the question is where the user thinks.
- Keep the preamble brutally short. Acknowledge the user's context in 1-2 sentences before asking your question. No walls of text.

## When the problem is sharp

You do not decide when the thinking is done — the user does. Stay read-only and keep to one question until they say the problem is understood and it is time to act. Only then drop the mode and implement, or hand back to the normal flow.
