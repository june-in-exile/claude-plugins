---
name: whiteboards
description: Use when the user asks to explain, map, or visualize a complex concept (a software system, codebase, architecture, paper, protocol, process) on a Heptabase whiteboard using cards, connections, and colors.
---

# Explaining a Concept on a Heptabase Whiteboard

## Overview

A whiteboard explanation is one card per component, placed on a uniform grid so every important relationship is a short, straight, labeled arrow between grid neighbours, with color encoding each card's role. The Heptabase manual owns the general layout rules. This skill is a house style that overrides four of the manual's defaults on purpose:

| Manual default | This style | Why |
|---|---|---|
| `fitToContent` cards | Uniform `setSize` cards | Equal cards make a grid where every neighbour edge is straight. |
| Build one region at a time | Build all cards in one pass (up to about 20 cards) | The grid plan already fixes every position. Above about 20 cards, build region by region. |
| Use `point` only for the first anchor | One `point` move per card | The grid plan gives exact top-left corners. |
| Labels are uncommon | Label every edge | The labels carry the explanation: what passes from A to B. |

## Before planning

1. Read the manual pages `concepts`, `whiteboard_visualization`, `whiteboard_layout`, and `hepta_markdown`.
2. Work out the reader from the conversation. Ask at most one question, and only if the reader's goal would change which cards exist.
3. Find the target with `list_whiteboards`. For a new board, call `create_whiteboard` under the parent that holds related boards, titled with the subject. If the user names a reference board, or a sibling board already explains something, call `read_whiteboard_layout` on it and copy its card size, spacing, and color meanings instead of the defaults below.
4. Gather evidence for every edge and every label. For code, the evidence is an import or call you grepped. For a paper, it is the section that states the relationship. Draw only the edges you can cite. The evidence stays in your working notes; the card carries the relationship it proves (see Card content).
5. Trace each component past the subject's own boundary: read the sibling boards and the neighbouring repos that consume it. Where it feeds another system, that hand-off is a relationship the card states, and an edge to that system's board (see Links to other boards).

## Edge labels

A label is a verb phrase of 12 characters or fewer naming what passes between this one pair. The source card's whole role lives in the source card's text, not in the label.

A label is a claim of its own: read it as "<target> gets <label> from <source>" and find that in the target's code. An edge can be real while half its label belongs to a different pair.

An "and" in a label (與、和) is the signal to check each half on its own:

- Both halves hold at the target: keep one edge and keep both in the label. Two straight edges between the same pair overlap, so never split them into two.
- One half belongs to another pair: drop it from this label. That pair gets its own edge, or already has one.
- The halves run in opposite directions: one `twoWay` edge whose label names both.

## Plan on a grid before creating anything

Write the plan as a table: each card with its column and row, and each edge with its endpoints, sides, and route.

- **Entry.** The overview card goes at the far left of the main row. Nothing goes below it, so its height can grow freely.
- **Main path.** The middle row runs left to right, in the order that data or control flows.
- **Support.** A supporting card goes directly above or below the card it serves.
- **Edge direction.** An arrow points the way the thing it names moves: data, a call, a token, a dependency. A support card points to the card it serves.
- **Edges join grid neighbours.** If an edge would cross other cards, move cards first: put a card that talks to two others where both are its neighbours, for example one row above and between them.
- **One instance per component.** A second instance makes a component read as a second step or a second system, so the reader sees the flow pass through it twice. A shared component (a registry, a store, a scheduler) goes in the middle as a hub, with one edge per relationship.
- **Edges that cannot be neighbours.** Three mutually connected cards cannot all be grid neighbours, and neither can a card with more partners than sides. In order of preference:
  1. Route the extra edge as an `elbow` with control points along an empty gutter around the cards in the way, for example under a row and up the next column gap.
  2. Place a second instance of the component, and say why in the overview card.
  3. Drop the edge and state the relationship in the card text.
- **Links to other boards.** A component that hands off to a system explained on another board gets an edge to that board: place a whiteboard shortcut (`create_whiteboard_shortcut`) beside the component, then connect the card to the shortcut with a labeled edge.

**Default geometry (no reference board).** A `point` destination is the card's top-left corner.

| Setting | Value |
|---|---|
| Card | 520×720, `setSize` |
| Overview | `fitToContent`. A single resize call can mix this with `setSize`. |
| Column x | 240 + 880·n, so 240, 1120, 2000, 2880, … (gap 360) |
| Row y | -20 + 920·n, so -20, 900, 1820, … (gap 200) |

The gaps leave room for labels: at 160 the labels cover the arrowheads. If a label still covers an arrowhead, widen that column gap with a `delta` move and keep the label. If a card's content does not fit in 720, split it into two cards, or raise the height of every card in that row.

## Card content

Write every card in the user's language. The title is an H1, `<plain name>（<code or term>）`. The body runs from summary to detail, in this order, and ends on the last detail bullet:

1. **Summary**: one or two sentences saying what entity or concept this card IS, with one concrete example from the subject. The reader should be able to stop here and still place the card.
2. **Naming caveat**, when the code name misleads or the thing goes by another name elsewhere: map every name the reader will meet (UI label, code name, the card title in the user's language) and say what the thing really is, as a sub-bullet under the summary. For example, a `Webhook` the UI lists as INTEGRATIONS, or a 投遞紀錄 card whose UI tab is Activity. A reader who knows the product from its UI cannot place a card titled with a translated or code name until the UI name is on it.
3. **Relationships**: how this card relates to its neighbours, stated with cardinality and trigger. Cardinality: "one webhook has many deliveries; each delivery sends one event to one URL." Trigger: what sets it in motion (a scheduler, a button, another card). This is the part the reader came for; the edges show that a relationship exists, the card says what shape it has.
4. **Detail**: what it holds or does, as bullets in the reader's words, each one placed under the sentence that introduces it.

Cards stay **conceptual**: architecture and ideas, the altitude a person explains at a whiteboard. Each sentence says what a part is, why it exists, or how it relates to another part. The evidence behind a card (file paths, line numbers, ADR and issue numbers, commit hashes, function, field and config names, environment variables, exact table counts) stays in your working notes: it goes stale with every commit, and the reader never opens it. When a detail matters to the idea, say it as a concept: "the secret is stored encrypted", not the field that stores it.

Every card is **grounded**: each term it uses is either defined on this card or is the title of another card on the board. Mention that card (syntax in `hepta_markdown`) instead of re-explaining it, which also shortens the sentence: "it only says where to send; sending is @Delivery's job". A term that is neither, cut, together with the sentence that needs it. An implementation detail the reader cannot place (an internal cache table, a field path whose parent was never introduced) is noise, however true.

Grow detail progressively: introduce a concept in a sentence first, then expand it in sub-bullets under that sentence. A sub-bullet may also define a term the sentence above just used, when no card on the board defines it.

The overview card states:

- the central question
- the regions
- the reading path
- the color legend
- the reason for any second instance of a component

A grounded card, for reference:

```
# 通知設定（Webhook）

本質上是一筆「事件發生時要通知誰」的設定，例如訂單成立時通知倉庫系統。
  - 名字容易誤會：介面上它列在 INTEGRATIONS 底下，不叫 Webhook。
一筆設定可以產生多筆 @投遞紀錄（Delivery），每筆把一個事件送到它的網址。
  - 事件是系統裡發生的一件事，例如訂單成立。
按下 Add webhook 會建立一筆設定，記三件事：
- 要通知的網址
- 勾選了哪些事件
- 簽章用的密鑰，加密存放
它只描述要送去哪裡，自己不送。
```

## Build order

1. `create_object` for every card. Keep the ids.
2. One `place_whiteboard_objects` call that puts all cards at one point.
3. One `resize_whiteboard_objects` call.
4. One `move_whiteboard_objects` call, with a `point` destination per card taken from the plan.
5. `create_connections` in batches of at most 6. Each connection sets the facing `from.position` and `to.position` and the label from the plan. It uses `routeType: "straight"`, except the `elbow` edges in the plan, which get their gutter control points.
6. `update_whiteboard_objects_color` for the cards and the connections, using the connection ids returned in step 5.
7. Call `screenshot_whiteboard`, then `lint_whiteboard`. Repair and check again until lint reports no issues and the screenshot shows no edge crossing a card and no label covering an arrowhead.
8. Call `render_cards` on the overview card if the client supports it. Otherwise describe the reading path in text.

Create and place calls are not idempotent: each place call adds a new instance. After a failure or timeout, call `read_whiteboard_layout` before you retry.

## Color by role

Pick 3 or 4 roles from the table below, or define your own that fit the subject. Give each role one color from the tool's set: yellow, red, blue, green, black, orange, purple, white. Write the legend in the overview card; a card whose role is not in the legend stays uncolored.

| Role | Color |
|---|---|
| Entry or overview | yellow |
| Main path | blue |
| Core mechanism, or state and storage | green |
| Guards, constraints, failure | orange |
| Context outside the subject (motivation, results, external systems) | purple |
| A card reused from another explanation | none |

A connection takes the color of the flow it belongs to.
