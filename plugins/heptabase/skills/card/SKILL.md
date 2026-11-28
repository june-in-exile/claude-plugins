---
name: card
description: Create a new Heptabase note card from what the user describes (summarize an article, write up notes, capture a decision), or update an existing card by title to reflect current progress from this conversation. Use whenever the user types /heptabase:card, or asks to write, save, summarize, or record something "into a card" / "到卡片" / "寫成卡片", or to update/sync a named card ("更新 \"Working Schedule\" 至當前進度", "把進度同步到某某卡"), even if they don't say Heptabase explicitly.
---

# Heptabase Card

Two jobs: **create** a note card from the user's description, or **update** an existing card so it matches what has happened in this conversation. Card content is always written in the user's language.

## Before any Heptabase call

1. Read the manual pages `concepts`, `knowledge_base_organization`, and `hepta_markdown` with `read_system_manual_page` (once per conversation). The Heptabase connection requires `concepts`; the other two hold the placement rules and the syntax you write in.
2. Call `read_space_instructions` once and follow it. The user's Space Instructions beat this skill's defaults.
3. Read the observations log (path under "Growing this skill"), if it exists. It lists situations earlier runs hit that this skill doesn't cover yet (see "Growing this skill").

## Pick the mode

- **Update** when the request names an existing card, usually in quotes, or says 更新 / 同步 / update / sync a card: `更新 "Working Schedule" 至當前進度`.
- **Create** otherwise: `摘要這篇文章`, `把剛剛的討論寫成卡片`.
- If it could be either (the request names a title that may or may not exist), search for the title first: a match means update, no match means create. Say which you chose in one line.

## Create a card

1. **Get the source.** Use what the request points at: an article or text in the conversation, a URL (fetch and read the page), a local file, or a Heptabase object (find it with `search_by_keyword`, then `read_object` / `read_pdf`). If "這篇文章" has no clear referent, ask which one; don't guess.
2. **Write the card** in the card style below. Start with an H1 title that names the subject, not the task ("Rust 的所有權模型", not "文章摘要"). Then:
   - the body the user asked for. For a summary, the default shape is: a one-line core idea, then the key points as nested bullets (each a claim, not a topic label), then any concrete numbers or examples worth keeping. For a concept card: structure, its defining trait, pros and cons, the flow if there is one, then examples. Adapt the shape when the material calls for it (a table for comparisons, steps for a procedure).
   - Cite Heptabase sources after each paragraph or bullet they support, using the citation syntax in `hepta_markdown`. Don't add a source or reference line for web pages or the conversation.
   - When the material spans several topics, split it into 2–3 focused cards rather than one long card or one card per detail.
3. **Create it** with `create_object` (`objectType: "card"`).
4. **Place it** in the same reply, following the placement order in `knowledge_base_organization`: a destination the user named → a Space Instructions rule → a high-confidence placement (place it and say so in one sentence) → otherwise propose 2–3 real whiteboards or tags with a one-line reason each, "or tell me where you want it". After the user picks one, place it and offer once to save that choice as a placement rule in Space Instructions. Once it is on a whiteboard, connect it to its closely related cards there (see "Connecting cards on a whiteboard").
5. **Show it**: call `render_cards` when the client supports it; otherwise give the title and a short outline in text.

## Update an existing card

### 1. Find exactly one card

Search with `search_by_keyword` (the quoted title, `objectTypes: ["card"]`), then keep the results whose title matches the name. Compare titles after removing Markdown escapes: a card titled `[Draft] Plan` reads back as `\[Draft] Plan`.

- **One match**: use it.
- **No match**: try `search_by_semantic` with the name. Show up to 3 close titles and ask which one, or whether to create a new card instead. Don't edit a card the user didn't confirm.
- **Several cards share the name**: never pick one yourself; the wrong card gets silently overwritten. Call `list_cards` with their IDs and `include: ["whiteboards", "timestamps"]`, read the opening lines of each, and ask the user with a short table:

  | # | Whiteboard(s) | Last updated | Starts with |
  |---|---|---|---|
  | 1 | Projects / Q4 Launch | 2026-11-14 | "## 本週 - [x] API spec …" |
  | 2 | (not on a whiteboard) | 2026-06-02 | "舊版排程：…" |

  Then wait for the answer.

### 2. Work out what changed

Read the whole card with `read_object`. Compare it with this conversation and list only changes the conversation actually supports:

- items now done (tick the todo, change the status);
- items whose plan, date, or owner changed;
- new items that belong on the card, in the section where they fit;
- items now blocked or dropped, with the reason when one was given.

Don't invent progress, and don't touch parts the conversation says nothing about. If a change is ambiguous (was it finished, or only started?), list it as a question rather than writing it.

### 3. Show the diff, then write

Before writing, show the planned changes in chat, grouped by the card's sections:

```
## 本週
- [ ] 完成 API spec  →  - [x] 完成 API spec
+ - [ ] 寫 migration script（新增）
## 風險
- 「Staging 環境未就緒」→ 刪除（已於 11/15 解決）
```

That code block is for plain-text clients (a terminal). In a graphical client that can render widgets, such as the Claude desktop app, draw the diff as a widget instead, because a monospace diff of long Chinese lines is hard to read there:

- one row per change, numbered; a rewrite shows the old text struck through above the new text, an addition is marked as new;
- a checkbox on every row, checked by default, so the user only unticks what they don't want;
- one submit button that sends back, in a single reply, the numbers to write and the numbers to skip.

An unchecked change is not written. A change the user didn't touch counts as allowed.

In a plain-text client, ask "要照這樣寫入嗎？" and wait. After a yes (or, with the widget, after the submitted selection), apply every allowed change in one `edit_note_card_content` call, using `replace` edits anchored on short, unique spans copied from the read (not `overwrite`, so the parts you didn't touch, including protected regions, stay byte-identical). Keep the card's existing structure, wording style, and language. If the user adjusts the diff, apply their version.

Then `render_cards` (or a text summary) to show the result.

## Writing rules for both modes

- Follow the Hepta Markdown syntax; links to other cards use mentions with IDs from a tool result, never invented IDs.
- No color unless the user asks for it.
- A write that fails or times out may already be applied: read the card again before retrying, and never repeat `create_object` blindly, because each success makes another card.
- After writing, say it's done in one line. Don't recap what changed; the user reads the card herself.

## Card style

The user keeps cards as compact study notes, not prose. Write this way unless Space Instructions say otherwise.

- **Nested bullets, one point each.** Short items with sub-items. Drop connective filler (「這就叫…」「所以…」) and trailing periods.
- **Half-width parentheses** with a space before them: `寫入墓碑 (tombstone)`, never `（）`.
- **Side notes go in a quote block** (`> 補充：…`), so the main bullets stay the core idea.
- **No source or reference line** at the bottom of a card.
- **End with an arrow mention** instead of a "see X" sentence: `→ <hepta-mention …>Related card</hepta-mention>`.
- **Mention every keyword that has its own card**, wherever it appears (WAL, fsync, B tree …). Before writing, list the cards on the same whiteboard or section (`read_whiteboard` in structure mode) so you know which keywords have cards; a keyword left as plain text is a broken link in her knowledge base. The same list tells you which cards to connect on the whiteboard.
- **No duplicated content across cards.** Each idea lives in the one card it is most about; other cards mention that card instead of re-explaining it. When a new card takes over a topic, replace the old explanation elsewhere with a mention.
- **Brief.** Cover the core ideas, not every detail; a lesson or topic is usually 2–3 cards.
- **Diagrams help.** When a mechanism is easier to see than to read (a data structure, a flow between memory and disk), offer a diagram to go in the card.

## Connecting cards on a whiteboard

When you place a new card on a whiteboard, connect it in the same run to the cards there it is closely related to: a card it extends, a card whose problem it solves, or a card it is a step of (e.g. SSTable → Compaction, SSTable → Bloom Filter). A mention in the text is not enough; the whiteboard should show the relationship too.

- Give each connection a short label for the relationship ("滿了寫出", "讀取時跳過"), matching the labels already on that whiteboard.
- Only connect close relationships; don't connect every card that mentions another.
- Use `routeType: "curve"` by default. The `whiteboards` skill keeps its own straight-line grid style.

## Growing this skill

This skill should grow from real use. The copy you are reading is an installed plugin copy that every plugin update overwrites, so never edit it, and keep the log outside it:

- **Log:** `${CLAUDE_PLUGIN_DATA}/observations.md`, the plugin's own data folder, which survives updates. If that path appears literally as `${CLAUDE_PLUGIN_DATA}` instead of a real folder, use `~/.claude/plugins/data/heptabase-june-in-exile/observations.md`.
- **Source:** the skill's source lives in the GitHub repo `june-in-exile/claude-plugins`, at `plugins/heptabase/skills/card/SKILL.md`.

At the end of each run, ask yourself whether this run hit a card-related situation the skill didn't cover: you had to improvise a decision, the user corrected how you did something, or the request was a new kind of card work (merging two cards, writing into the journal, splitting a long card, a recurring card template such as a weekly review).

When it did:

1. Append one line to the log (create it if missing): `YYYY-MM-DD | situation | what you did | what the user wanted`. The log is how a later run can tell a situation recurs, since each run starts with no memory of earlier ones. Read it at the start of a run (see "Before any Heptabase call").
2. If the same kind of situation already appears in the log, or the user's correction is clearly a lasting preference, propose adding it to this skill. Put the proposal at the end of your reply: the section it goes in and the exact text to add, in a few lines.
3. When the user says yes, apply it to the source, not the installed copy:
   - If the current working directory, or a folder the user names, is a clone of `june-in-exile/claude-plugins`, edit `SKILL.md` there and bump the patch `version` in both `plugins/heptabase/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`. Committing and pushing are the user's call. Once it is pushed, `claude plugin marketplace update june-in-exile` then `claude plugin update heptabase@june-in-exile` installs it, effective from the next session.
   - Otherwise, give the change as a ready-to-paste block and say where it goes, so the user can apply it in their clone or open an issue or pull request on the repo.
   - Either way, remove the log lines the change now covers.
4. A one-off quirk that won't recur gets neither a log line nor a proposal.

Keep proposals general: write the rule and its reason, not a script for one card.
