---
name: gogogo
description: Take one feature from idea to implementation-ready slices. Grill the idea, record it as an OpenSpec change, slice it, then hand each slice to pstack's poteto-mode in a new session.
disable-model-invocation: true
---

# Gogogo

Take one feature from an idea to slices that are ready for poteto. Steps 1 to 4 run in this one context window. If it fills up mid-way, run `/develop:handoff` and continue in a new session; never compact in the middle.

**The repo's own rules win.** Before step 1, read the repo's `CLAUDE.md` / `AGENTS.md` and anything it points at for: where ADRs live, where the glossary lives, the issue tracker and its labels, and the verification commands. Where this skill and the repo disagree, follow the repo and say so in one line.

## 1. Grill

Call the Skill tool with `mattpocock-skills:grilling`, then `mattpocock-skills:domain-modeling` (this pair is what `grill-with-docs` runs).

Write an ADR only when all three hold: hard to reverse, surprising without context, a real trade-off. Keep each one to the decision and its reason in a few lines.

Done when every open question has a human answer, and the glossary and ADRs written in this step are saved.

## 2. Record the spec as an OpenSpec change

Check for an OpenSpec root with `openspec list --json` (`"root": null` means none). With no root, ask the human whether to run `openspec init --tools claude` here or fall back to `/mattpocock-skills:to-spec`; that choice is theirs.

Call the Skill tool with `openspec-propose`, telling it:

- The reasons live in the ADRs from step 1. Cite them by number; copy nothing.
- `proposal.md` stays under 300 words and ends with a list headed "Decisions for the human".
- At most three scenarios per requirement. Existing behaviour that only becomes a baseline goes in its own requirement, apart from what this change adds.
- One task group in `tasks.md` per ticket-sized vertical slice.

Done when `openspec validate <change> --strict` passes.

## 3. Human review

Show the human only these: the proposal's "Decisions for the human" list, every `### Requirement:` title, and the test seams named in `design.md`. Revise with `openspec-update-change` until they approve.

Done when the human has approved the seams in words.

## 4. Slice, and open tickets only when they earn it

Find the to-tickets skill with `find ~/.claude/plugins/cache -path '*mattpocock-skills*/to-tickets/SKILL.md'` (take the highest version) and read it in full. It is user-invoked only, so the Skill tool cannot load it.

Always apply its slicing to the task groups in `tasks.md`: each group is a vertical slice through every layer, verifiable on its own, small enough for a fresh context window, and names the groups that block it.

Open tracker issues only when one of these holds, and say which in one line:

- someone else needs to see or coordinate the work
- the change spans several repos
- it will run across many PRs or several days
- the repo's rules require tickets

Otherwise `tasks.md` is the only record of progress; tick it as work lands. When issues are opened, write each number into the heading of its task group, and from then on the issue's state is the progress record.

Done when every task group is a slice with its blockers named, and, if issues were opened, each group names exactly one.

## 5. Hand off to poteto

Commit and open the docs PR only if the human asks.

Print one fenced block per slice (per issue when issues were opened), in blocking order, for the human to paste into a new session they open themselves. A session card does not work here: the app prepends a background section to a card's prompt, so the slash command is not at the start and never fires (tested 2026-11-11).

```
/pstack:poteto-mode Implement <slice: task group N, or #issue>.

The design is settled in openspec/changes/<change>/ (proposal.md, design.md, specs/).
Keep the Feature playbook's how and architect steps inside design.md.
If the design itself is wrong, stop and report; leave the spec as it is.

Scope: only that task group in tasks.md; tick each task as it is done.
Repo rules:
- /mattpocock-skills:tdd at the seams design.md lists
- /mattpocock-skills:code-review before opening the PR
- the repo's verification commands before claiming done
- if there is an issue, the PR body says Closes #<issue>
```

Add any repo rule from the pre-step that poteto would otherwise miss (wording rules, coverage ratchets, ports to avoid).

End by telling the human: once every slice is merged, run `/opsx:archive <change>` in its own PR, so `openspec/specs/` describes the system again.
