---
name: brainstorm
description: When you hit a blocker or wall during development, spin up a dedicated research workspace to brainstorm solutions. Triggers on phrases like "brainstorm this", "I'm stuck", "hit a wall", "need to research this blocker", "brainstorm solutions", "spin up a research space for this".
---

# Brainstorm Solutions

When the user hits a blocker, create a dedicated research workspace seeded with full context about the problem, then offer to kick off deep research.

## Configuration

Check for a `config.json` at the plugin root (same directory as `.claude-plugin/plugin.json`). It supports:

```json
{
  "research_index_repo": {
    "local_path": "~/repos/github/my-repos/Open-Research-Workspaces-Index",
    "github_url": "https://github.com/user/Open-Research-Workspaces-Index",
    "enabled": true
  },
  "research_template_path": "~/repos/github/my-repos/Claude-Research-Workspace-General-Template",
  "research_output_path": "~/repos/github/my-repos"
}
```

- `research_index_repo` — if `enabled` is true, the new workspace will be registered in this index repo after creation.
- `research_template_path` — path to the research workspace template to copy from. Falls back to `~/repos/github/my-repos/Claude-Research-Workspace-General-Template`.
- `research_output_path` — where to create the new repo. Falls back to `~/repos/github/my-repos`.

If `config.json` is missing or fields are empty, use the defaults and skip the index step.

## Inputs to gather

1. **Blocker summary** — what's the wall? One or two sentences.
2. **What was tried** — approaches attempted so far and why they didn't work.
3. **Potential ideas** — any hunches or directions worth exploring (optional).
4. **Repo name** — suggest a Title-Case-With-Hyphens name derived from the blocker topic (e.g. `Local-Plugin-Install-Research`, `Wayland-Screen-Sharing-Fix`). Confirm with the user.
5. **Visibility** — public (default) or private.

## Procedure

### 1. Scaffold from research template

```bash
cp -r ~/repos/github/my-repos/Claude-Research-Workspace-General-Template \
      ~/repos/github/my-repos/<Repo-Name>
rm -rf ~/repos/github/my-repos/<Repo-Name>/.git
```

Repo name MUST be Title-Case-With-Hyphens.

### 2. Seed the research brief

Overwrite `context/from-human/research-brief.md` with the captured context:

```markdown
# Research Brief

## Blocker

<blocker summary>

## Date Captured

<today's date, YYYY-MM-DD>

## What Was Tried

<bullet list of approaches attempted and why each failed or fell short>

## Potential Directions

<bullet list of ideas or hunches worth exploring>

## Key Questions

1. <derived from the blocker — what specifically needs to be answered?>
2.
3.

## Desired Output

A clear recommendation with supporting evidence — ideally actionable steps the user can take immediately.
```

### 3. Seed an initial research prompt

Create `prompts/queue/01-initial-investigation.md`:

```markdown
# Initial Investigation

## Objective

Research solutions to the following blocker:

> <blocker summary>

## What's Already Been Tried

<what was tried>

## Research Instructions

1. Search for official documentation, GitHub issues, forum threads, and blog posts related to this problem.
2. Identify all known approaches — even unconventional or experimental ones.
3. For each approach, assess: feasibility, complexity, trade-offs, and whether it's a permanent fix or a workaround.
4. Pay special attention to recent developments (last 6 months) — the landscape may have changed.
5. If relevant, check if there are open feature requests or RFCs that would solve this natively.

## Output Format

Structure findings as:
- **Approach name**: one-line summary
  - How it works
  - Pros / cons
  - Complexity (low / medium / high)
  - Links / sources
```

### 4. Customize CLAUDE.md

Lightly update the workspace CLAUDE.md to reference the specific blocker topic in the opening section. Don't rewrite the whole file — just replace the generic "research workspace" language with the actual topic.

### 5. Init and push

```bash
cd ~/repos/github/my-repos/<Repo-Name>
git init
git add -A
git commit -m "Initial scaffold: brainstorm solutions for <blocker topic>"
gh repo create danielrosehill/<Repo-Name> --<public|private> --source=. --push
```

### 6. Register in research index (if configured)

If `config.json` has `research_index_repo.enabled` set to `true`:

1. Navigate to the index repo's local path.
2. `git pull` to get latest.
3. Read the `README.md` to understand the entry format.
4. Add a new entry under the `## Research Workspaces` section, matching the existing format:

   ```markdown
   ### <Repo-Name (spaces instead of hyphens)>

   <One-sentence description of the blocker/research question in the format: "Research workspace exploring X. **Question:** Y?">

   [![View Repo](https://img.shields.io/badge/View-Repo-blue?style=flat&logo=github)](https://github.com/<user>/<Repo-Name>)
   ```

5. Insert alphabetically among existing entries.
6. Commit and push:
   ```bash
   git add README.md
   git commit -m "Add <Repo-Name> to research index"
   git push
   ```

If not configured, skip this step silently.

### 7. Report and offer next step

Tell the user:
- Local path: `~/repos/github/my-repos/<Repo-Name>`
- GitHub URL
- The workspace is seeded and ready

Then ask: **"Want me to open a new Claude session there and start the deep research now?"**

If yes, use the `session-transfer:new-claude-at` skill to open a new Claude Code session at the new repo path.
