# Brainstorm Solutions

When you hit a wall during development, spin up a dedicated research workspace to brainstorm your way through it.

## How it works

1. You're stuck on something — a technical blocker, a design decision, an integration problem.
2. Invoke `/brainstorm-solutions:brainstorm` and describe the blocker.
3. The plugin creates a research workspace repo (from the Claude Research Workspace template), seeded with your blocker context, what you've already tried, and potential directions.
4. Jump into the workspace and run `/brainstorm-solutions:deep-research` to kick off structured investigation.

## Skills

| Skill | What it does |
|-------|-------------|
| `brainstorm` | Capture the blocker, scaffold a research repo, seed it with context, push to GitHub |
| `deep-research` | Run structured research inside the workspace — web search, documentation lookup, synthesis |

## Configuration

Edit `config.json` at the plugin root to customise paths:

```json
{
  "research_index_repo": {
    "local_path": "~/repos/my-research-index",
    "github_url": "https://github.com/you/your-research-index",
    "enabled": true
  },
  "research_template_path": "~/repos/my-research-template",
  "research_output_path": "~/repos"
}
```

- `research_index_repo` — optionally register each new workspace in a research index repo (set `enabled: false` to skip)
- `research_template_path` — local path to your research workspace template
- `research_output_path` — where new research repos get created

## Installation

```bash
claude plugins install danielrosehill/brainstorm-solutions-plugin
```

## License

MIT
