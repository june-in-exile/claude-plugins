---
name: poteto-review
description: Review a feature the user just finished through poteto's (Lauren Tan's) agent-first development philosophy, and find where the workflow keeps trust from scaling. Use only when the user explicitly types /poteto-review or explicitly asks to "review this from poteto's perspective"; do not trigger it just because the user finished a feature, mentioned code review, or mentioned agents.
---

# poteto-review

After the user finishes a feature, use the development philosophy poteto has expressed publicly to examine *how the feature was built*, not just the code itself. The goal is to answer one question: **next time we do the same kind of work, can we more confidently hand it to more agents in parallel?**

This review uses her public views as a mirror. It is not an imitation of her voice. Never invent quotes or experiences attributed to her.

## Core beliefs (the basis of the review)

1. **Trust is the bottleneck, not the number of agents.** Without enough trust, adding agents just means humans spend more time reviewing code, and nothing parallelizes.
2. **Corrections become deterministic checks.** Every time a human corrects an agent, that correction should become a lint rule, a type, a test, or an API that makes the mistake impossible, not one more line in a rules file or AGENTS.md. Text gets ignored; checks don't.
3. **Agents copy what the codebase already does.** Bad patterns must be removed, not discouraged. The shortest path must be the correct path.
4. **Verification beats code review.** The ideal thing to review is a verification artifact: test results, screenshots, recordings, a running flow, a formal check. Agents should be able to run the app and confirm the result themselves.
5. **Constraints have layers of reliability (earlier is more reliable):** codebase structure → static analysis (lint / compiler / CI) → rules / review bot → skills → style guide.
6. **Slop in, slop out.** If the inner development loop produces poor results, automating it only produces more garbage. Fix the loop first, then scale.
7. **Automate maintenance, keep judgment human.** Bug fixes, performance, and maintenance suit automation; feature and experience decisions should be designed deliberately by people.
8. **Cross-model review.** Having different models find each other's mistakes is more reliable than one model checking itself.

## Process

### 1. Gather facts (look first, don't ask first)

Get as much evidence from the environment as you can, and ask the user only for what you can't find:

- The diff / PR / commit history for this feature (`git log`, `git diff`, etc.)
- The project's lint config, type config, CI config, and tests
- Rules files, AGENTS.md, CLAUDE.md, skills
- In this conversation or the agent run logs, **the places where the user corrected the agent** (this is the most important signal)

If key information is missing, ask at most one question, for example: "During this work, what did you correct the agent on most often?"

### 2. Review along six dimensions

For each dimension, find concrete evidence; don't speak in generalities:

| Dimension | Question to ask |
|-----------|-----------------|
| **Corrections → rules** | Which corrections this time stayed in the conversation or in rules text and never became a check? |
| **Verification loop** | Can the agent prove on its own that the feature works, or does it come down to a human eyeballing it? |
| **Agent-friendly architecture** | When an agent copies nearby code, does it copy a good pattern or a bad one? Is there anywhere a shortcut is easier than the right way? |
| **Constraint layers** | Which layer do the important rules live in? Is anything that belongs in lint or types only written in docs? |
| **Loop quality** | Are there signs of repeated rework or back-and-forth patching? Is the loop itself healthy before automation scales up? |
| **Human / machine split** | Were experience decisions that need human judgment handed to the agent? Or, the other way round, is a human doing maintenance work that should be automated? |

### 3. Self-balance (required)

poteto's approach has blind spots too. Before concluding, check whether your suggestions fall into these traps, and say so plainly if they do:

- **Cost**: heavy verification burns tokens. Suggestions should fit the user's scale and budget; don't assume unlimited resources.
- **Verification blind spots**: tests only verify the properties you thought of. Remind the user of risks outside the spec (permissions, data access, privacy).
- **Loss of understanding**: don't suggest letting agents merge on their own unless verification is already very thorough; humans still need to understand the system.
- **Greenfield bias**: legacy systems can't always be re-architected. Prefer incremental approaches.

## Output format

Reply in the user's language, concisely, concretely, and encouragingly. Use this structure:

```
## One-line diagnosis
[The biggest trust bottleneck in this workflow]

## What went well
[1–3 points, concrete]

## Problems and suggestions
[Ordered by impact, at most 5. Each includes:]
- **Problem**: [the concrete evidence you saw]
- **Why it matters**: [which core belief it maps to]
- **Next step**: [the smallest workable change, preferring "turn it into a deterministic check"]

## Corrections that can become rules
[List the corrections from this work that could become lint / types / tests; if none, write "None found this time"]

## Blind spots this time
[The limits found in the step 3 self-balance]

## If you change only one thing
[If only one thing can change, change this]
```

## Principles

- Evidence first: every problem must point to a specific file, commit, or piece of conversation. If you can't find evidence, don't make it up.
- Less is more: 3 real problems beat 10 generic suggestions.
- Don't write code the user didn't ask for. You may describe what a rule should check, and implement it once the user agrees.
