---
title: Claude Code optimizations
author: Sergio Romano
date: 2026-06-19
layout: post
---

Last week I had to give a talk about AI and Claude, specifically focusing on Claude Code. My main goal was to introduce something that people often forget when working with AI: we need to learn *how* to build with it, and we can only do that if we focus on solving real-world problems like cognitive overload and token waste. 

Sometimes, that "how-to" reduces to simple mental models. To kick things off, I wanted to take a different approach. My first thought was a compiler: we receive artifacts (inputs) and we transform them into production code (outputs).

## The Trap

But to get there, we first need to understand the problem—the trap.

![Image](/posts/resources/mr_burns_diseases.webp)

This image is from The Simpsons: it's Mr. Burns' diseases all fighting *through* the door to enter his body. They look completely stuck; they can't do anything because there are so many of them trying to squeeze in at the same time. 

Those are your agents and your context window. Mr. Burns believed he was invincible, but that wasn't the case. Similarly, when we use AI agents, we often believe we are invincible, but that's a dangerous illusion. 

## The 4-Step Flow (The Mental Model)

No matter what agent you are using, if we abstract a clean, minimal workflow to prevent this bottleneck, it should look like this. Just 4 steps—continuous, repeatable, and designed to avoid errors whether you are using a CLI coding agent or a standard chat interface:

```bash
/prompt What you want

/plan&execute What you need

/summary To understand what just happened

/compact Condense your conversation so you can keep working
```

I have a hypothesis that having a mental model can save your context. So let's go one step back.

## Foundations

![Image](/posts/resources/anthropic-prompting-context-engineering.webp)  
*Source: [Anthropic - Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)*

Hallucinating AI, a human brain exploding because there are too many open tabs and agents. Did you know that the human mind can actively process only about 7 things at once?

With recent advancements in AI, engineers started realizing that context is the key. We went from writing simple, single, one-shot prompts to understanding that we need to actively manage the entire context layer. The tools, the memory, the domain knowledge—everything must be curated for the sake of the task at hand. We are rapidly evolving from Prompt Engineering to Context Engineering, and right now, into Loop Engineering.

## Telemetry & Observability

What doesn't get measured doesn't exist, and what isn't understood can't be measured. To tackle token waste, we need to track a few critical variables: Input Tokens, Output Tokens, and the Context Window. The latter is simply the accumulation of both across a full, ongoing conversation from start to finish.

## Demo

Showtime

![Image](/posts/resources/showtime.jpg)

Remember when I said we are going to build a compiler? We will transform artifacts into code. How? We will take a simple, raw, one-shot prompt and use a structured process to optimize artifact by artifact until we get a calibrated prompt that hits the target on the first run.

### The Raw Input Prompt:

```bash
Goal: 
Implement the batch update function bulkUpdateUserTiers 
inside /src/services/billing.ts to apply pre-computed tier 
updates to our users in bulk.
```

### The Optimized Prompt

Steps: 
1. Keep the same goal, but start injecting structure.
2. Add tools: Introduce specific context (like using custom query builders or internal patterns).
3. Add domain knowledge: Inject a concrete ADR (Architecture Decision Record) like ./docs/adr/004-use-cte-for-aggregations.md to specify what query structures to use and which ones to completely avoid.
4. Exclude the noise: Set up a strict .claudeignore file.
5. Establish Quality Gates: Define a strict set of sequential gates that the agent must pass:
    - Gate 1: Static Boundary Compilation
    - Gate 2: The Reflection Loop - DB Insight
    - Gate 3: Edge-Case Test Loop
6. Apply Constraints: "No conversational fluff; just output the production-ready code after all gates succeed." 

![Image](/posts/resources/ai-talk-prompt-calibration.png)

**Results:**
| Metric | Unbounded | Optimized |
| Turns to Success | 2-3 | 1 |
| Execution Time | >15s (timeout) | 404-857ms |
| Strategy | N+1 loops | CTE + batch math |
| Cost | $0.03 | $0.05 |

The result? A production-grade, highly optimized code snippet that is ready to be reviewed and deployed. At the end the difference wasn't only the turns it took to make it work but also the quality to ensure that is correctly solving that problem even when scaling comes to the table.

Repo: [github.com/lessch4os/claude-optimization](https://github.com/lessch4os/claude-optimization)  
- `main` branch: the code  
- `results` branch: reproduction guide and benchmark results

Further Optimizations:
- Bake these rules directly into a local CLAUDE.md file.
- Create a dedicated "skill" or system prompt containing your specific database knowledge to refine the CLAUDE.md execution layer.

## Conclusion

- Having a mental model saves your context
- Prune context to prevent attention smear
- Trace semantic drift across agent handoffs
- Precise prompting, feedback loops & foundations understanding can save time & money

This insight was originally presented as an internal engineering talk by Sergio Romano. You can check his personal corner at sergio.lessch4os.com or connect via LinkedIn.

So...
What do you think about prompts?
What do you think about context?
What do you think about compilers?
What do you think about mental models?

Bye!
