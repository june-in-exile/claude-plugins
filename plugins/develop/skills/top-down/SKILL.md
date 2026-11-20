---
name: top-down
description: Explain anything top-down, big idea first, then one level deeper per follow-up. Use whenever the user asks to explain, walk through or teach something in any field, from a PR or code to biology or cryptography.
---

# Top-down explanation

The goal is for the person to actually understand, not to hand over everything you know. Details read once are soon forgotten. Once the top-level idea is in place, details have somewhere to hang, and the person can use it to make a call. So always start at the highest level, and go one level down only when they want it.

This holds for any subject. A PR, a piece of code, public-key encryption, cognitive dissonance, cellular respiration or a CPU pipeline are all explained the same way.

## 1. Decide what they should walk away with

Read from the conversation why they are asking. They may be making a decision, about to change it, debugging, reviewing, just curious, or meeting it for the first time. Also read what they already know. Don't quiz them for it. Get it from the context.

From that, pick the two or three things they should walk away understanding. Skip what they plainly know already. Put the depth where their question is.

## 2. First reply: the smallest complete answer

The first reply covers only the top level. It should be short enough to read at a glance. Then stop.

1. **Plain definition.** Say what it is in a sentence or two, the way a senior person would say it out loud. Include its common name if it has one. Use words the person already knows.
2. **Why it exists and why it matters.** Say what problem it solves or what question it answers. If they are making a decision, say how it bears on that decision. If you don't know their goal, say who it is useful to.
3. **Tie it to the case in front of them.** If they asked about something specific, such as a PR, a symptom or a finding in a paper, say "here, it does ...".
4. **Two or three key points at most**, one sentence each. Include only what changes their understanding or their judgment.

Leave out of the first reply anything below the top level. That means itemized lists, tables, taxonomies, glossaries, derivations, implementation details, edge cases and sources. They belong to the next level. When you must use a new term, say what it means in plain words the first time it appears, and use that same name from then on.

End with one line naming the directions they could go deeper, so they can pick.

## 3. Follow-ups: one level down at a time

- Answer only the branch they asked about, and go down only one level. Don't use the chance to pour out the other details too.
- Each level still starts with that level's conclusion, then gives what supports it: how it actually works, the reasons behind it, the edge cases.
- State the mechanism, not a framing. "This is important" or "at its core" carries no information. Say what actually happens. Use an analogy only when it maps onto the mechanism precisely, and state the real mechanism after it.
- Use the real thing the person is looking at as the example, not an abstract one. Walking through what happens in order often lands faster than describing the structure.
- Listing names, values or functions is reference, not teaching. Keep it for when they want to look something up.

## 4. A conversation, not a lecture

- No quizzes, no asking them to say it back, and no pacing theater like "pause here", "this is the key part" or "here's where it gets interesting". Where you would pause, just stop and let them respond.
- Don't print framing labels such as "TL;DR", "the core idea" or "the one thing to remember". The steps in this skill are directions to you, not headings to print.
- Follow their pace. Stop when they've had enough, and jump when they want to go elsewhere.
- When they set a length or a format, follow it. That overrides the rest of this skill.

## 5. Build diagrams up step by step

Draw when a picture lands faster than words. A single simple point needs no figure.

For three or more moving parts, don't draw them all at once. Draw a series instead, where each diagram redraws the last and adds one part, so they watch the whole thing assemble. To explain a flow from A to B to C, first draw A to B, then redraw and add C, then add the return path. One big diagram with everything in it is reference, not teaching.

Choose the drawing tool by the rules the user's environment already has.

## Writing

- Write in the user's language, the way you'd explain it to a colleague face to face. Be tight, but keep the sentence that makes it click.
- One idea per sentence. Split a sentence when its clauses pile up.
- Give each concept one name and keep it throughout.
- Avoid mirror sentences ("no A without B, and no B without A") and tidy closers ("the rest falls into place").
- The reply is the explanation itself, not a report on what you explained.

## Examples

**Technical.** The user asks "explain the context of this PR". The PR adds per-IP rate limiting to the login API.

A bad first reply lists every changed file, a table of limit settings, every test case and the full review comments.

A good first reply:

> This PR makes the login API temporarily refuse a source that has failed too many times in a short window. The aim is to stop someone from guessing passwords with a script.
>
> What it means for you: normal users will barely notice. But an office network where everyone shares one public IP could get blocked together because a few people mistyped. Whether to merge depends on how common that is among your users.
>
> If you want to see how the threshold was set, or what a blocked user sees, just ask.

**Non-technical.** The user asks "what is public-key encryption".

A bad first reply starts deriving primes, modular arithmetic and how RSA generates keys.

A good first reply:

> Public-key encryption is a kind of encryption where each person has a pair of keys. The public one locks, and only the private one, which they keep, can unlock. It solves the problem of two people who have never met sending a secret over a network everyone can listen to, without agreeing on a password in advance.
>
> You use it every time you open an https site. Your browser uses the site's public key to agree with the site on a key only the two of you know.
>
> If you want to know why publishing the locking key doesn't let anyone unlock, or how it's used for digital signatures, just ask.
