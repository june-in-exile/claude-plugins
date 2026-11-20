# claude-plugins

Claude Code plugins by [june-in-exile](https://github.com/june-in-exile), published as the marketplace `june-in-exile`.

## Install

```bash
claude plugin marketplace add june-in-exile/claude-plugins
claude plugin install heptabase@june-in-exile
claude plugin install workflow@june-in-exile
claude plugin install speak@june-in-exile
claude plugin install explain@june-in-exile
```

Install only the plugins you want. Inside Claude Code you can run the same commands as `/plugin marketplace add june-in-exile/claude-plugins` and `/plugin install heptabase@june-in-exile`.

To get later versions:

```bash
claude plugin marketplace update june-in-exile
claude plugin update heptabase@june-in-exile
claude plugin update workflow@june-in-exile
claude plugin update speak@june-in-exile
claude plugin update explain@june-in-exile
```

## Plugins

### heptabase

Skills for working with [Heptabase](https://heptabase.com). They need the Heptabase MCP connector to be connected in Claude.

- `/heptabase:card` — create a note card from what you describe (for example, "summarize this article"), or update an existing card by title to match the progress in your conversation (for example, `/heptabase:card 更新 "Working Schedule" 至當前進度`). It shows the planned changes before writing, asks which card you mean when several share a title, and suggests where a new card should go. When it meets the same uncovered situation more than once, it proposes an addition to itself; its log lives in the plugin's data folder, not in this repo.
- `/heptabase:whiteboards` — explain a complex concept (a system, codebase, paper, or process) on a Heptabase whiteboard with cards, labeled connections, and colors.

### workflow

Skills for the development process.

- `/workflow:gogogo` — take one feature from an idea to implementation-ready slices: grill the idea and record decisions (glossary, ADRs), write it up as an [OpenSpec](https://github.com/Fission-AI/OpenSpec) change, get your approval on the decisions and test seams, slice the tasks, then print one prompt per slice to paste into a new session. It runs only when you call it.

It builds on other skills, so install these too:

- the `mattpocock-skills` plugin from the `claude-plugins-official` marketplace (grilling, domain-modeling, to-tickets, tdd, code-review, handoff)
- the `pstack` plugin from the `cursor-plugins` marketplace (`/pstack:poteto-mode`, which implements each slice)
- the OpenSpec CLI and its Claude skills (`openspec init --tools claude`), which provide `openspec-propose`, `openspec-update-change`, and `/opsx:archive`

### speak

A skill for talking with Claude by voice. It needs macOS: replies are spoken with the built-in `say` command and the Taiwanese Mandarin voice Meijia.

- `/speak:voice-reply` — turn on voice replies. From then on Claude answers every message aloud in two to four short spoken sentences and shows exactly the same spoken-style text on screen. Ask it to stop and it switches back to text; ask again and it speaks again. It runs only when you call it.

### explain

A skill for explanations that start from the big picture.

- `/explain:top-down` — explain a PR, code, a design, or any concept top-down: first one sentence on what it is and why it exists, then what it means for your decision, then at most a few key points. Tables, file paths and code come only when you ask to go deeper, one level at a time. Claude also uses it on its own whenever you ask it to explain something.

## License

MIT. See [LICENSE](LICENSE).
