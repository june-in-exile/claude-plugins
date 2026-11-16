# claude-plugins

Claude Code plugins by [june-in-exile](https://github.com/june-in-exile), published as the marketplace `june-in-exile`.

## Install

```bash
claude plugin marketplace add june-in-exile/claude-plugins
claude plugin install heptabase@june-in-exile
```

Inside Claude Code you can run the same commands as `/plugin marketplace add june-in-exile/claude-plugins` and `/plugin install heptabase@june-in-exile`.

To get later versions:

```bash
claude plugin marketplace update june-in-exile
claude plugin update heptabase@june-in-exile
```

## Plugins

### heptabase

Skills for working with [Heptabase](https://heptabase.com). They need the Heptabase MCP connector to be connected in Claude.

- `/heptabase:card` — create a note card from what you describe (for example, "summarize this article"), or update an existing card by title to match the progress in your conversation (for example, `/heptabase:card 更新 "Working Schedule" 至當前進度`). It shows the planned changes before writing, asks which card you mean when several share a title, and suggests where a new card should go. When it meets the same uncovered situation more than once, it proposes an addition to itself; its log lives in the plugin's data folder, not in this repo.
- `/heptabase:whiteboards` — explain a complex concept (a system, codebase, paper, or process) on a Heptabase whiteboard with cards, labeled connections, and colors.

## License

MIT. See [LICENSE](LICENSE).
