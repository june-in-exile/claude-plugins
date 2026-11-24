---
name: handoff
description: Summarize this session's progress (what was done, what is in progress, what comes next) and send it to another Claude Code session so it can pick up the work. Usage is /develop:handoff <session-name> [focus of the next session].
argument-hint: "<session-name> [what the next session will focus on]"
disable-model-invocation: true
---

# Handoff

Write a handoff for a fresh session, save it to a file, and send it to the session the user named. The receiver starts with no context, and it reads the message literally, so the message has to stand on its own.

## 1. Read the arguments

- The first argument is the target session's name. Names can contain spaces. Match it against the `ListAgents` listing in step 4: the longest leading part of the arguments that exactly matches a listed name is the target. If none matches, treat the first argument (or the quoted part) as the intended name.
- The rest of the arguments describe what the next session will focus on. Tailor the handoff to that: put the depth there, and leave out finished work it won't touch.
- No arguments: write the file only (steps 2 and 3), print its path, and skip delivery.

## 2. Check the state, don't recall it

Run the commands instead of relying on memory of the conversation: `git status --short`, the current branch, `git log --oneline` for recent commits, and the commits not yet pushed (`git log @{u}..` or against the main branch when there is no upstream). Note uncommitted files, stashes, and running background tasks.

## 3. Write the handoff

Save it to a path from `mktemp -t handoff-XXXXXX.md`. Read the file before writing to it.

Start with one line that makes sense on its own, because the receiver's user sees only the first line as a preview: what the work is, and what the receiver should do next.

Then these sections, skipping any that would be empty:

1. **Load first.** Skills the receiver should load before anything else, and why. When a skill changed during this session, say how to tell the new version from the old one.
2. **Goal.** What the work is for, in two or three sentences.
3. **Done.** Point to commits, PRs, ADRs, plans and docs by hash, path or URL. Do not copy what they already say.
4. **In progress.** The exact state: branch, uncommitted files, what is half-written, what was last run and its result.
5. **Waiting on the user.** Every question the user has not answered yet, quoted as asked. This is the section most often lost.
6. **Learned this session.** Corrections and preferences the user gave, with their own words quoted in their own language; environment problems and how they were fixed; decisions and who made them (the user's decisions are not to be reopened).
7. **Next steps.** In order, the first one concrete enough to start on immediately.
8. **Verify before starting.** The commands the receiver should run to confirm the state matches this handoff (for example `git status`, `git log`, the test command). Tell it to trust what it sees over what the handoff says, and to say so when they differ.

Rules:

- Write in English, but keep the user's quoted words as they wrote them.
- Do not repeat what lives in other artifacts. Reference them.
- Never include secrets, tokens, email addresses or other personal details.
- Preferences meant to last beyond this work belong in memory, CLAUDE.md or a skill, not only in a handoff, or the next handoff loses them. Before writing, if this session produced such a preference and it isn't saved yet, offer once to save it; then reference where it lives.
- Keep it under about 800 words. The receiver can read the referenced files for detail.

## 4. Deliver it

Call `ListAgents`.

- **Exactly one listed session has the name**: send the full text with `SendMessage`. Paste the content itself; an `@path` attaches nothing on the receiving side. Add the file path at the end as a backup.
- **No exact match**: do not guess. Ask with `AskUserQuestion`, offering up to three of the most recently started sessions (name, how long ago it started, its kind) plus "It isn't open yet". If the user picks a session, send to it, and tell the user to run `/rename <name>` in that session, since a session cannot rename itself.
- **Not open yet, or delivery failed**: print the file path and a line the user can paste into the new session: `Read <path> and continue the work it describes.`
- Never send to this session itself, and never to a session the listing does not show.

If a delivery notice says the receiver is holding the message for approval, tell the user to approve it in that session.

## 5. Report

Tell the user in two or three lines: which session got the handoff (or that it is only in the file), the file path, and anything they need to do there (approve the message, run `/rename`). Do not repeat the handoff's content.
