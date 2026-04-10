---
name: brainstorm
description: When you hit a blocker or wall during development, spin up a dedicated research workspace to brainstorm solutions. Triggers on phrases like "brainstorm this", "I'm stuck", "hit a wall", "need to research this blocker", "brainstorm solutions", "spin up a research space for this".
---

# Brainstorm Solutions

When the user hits a blocker, create a dedicated research workspace seeded with full context about the problem, then offer to kick off deep research.

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

### 6. Report and offer next step

Tell the user:
- Local path: `~/repos/github/my-repos/<Repo-Name>`
- GitHub URL
- The workspace is seeded and ready

Then ask: **"Want me to open a new Claude session there and start the deep research now?"**

If yes, use the `session-transfer:new-claude-at` skill to open a new Claude Code session at the new repo path.
