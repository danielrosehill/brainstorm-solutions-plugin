---
name: deep-research
description: Run deep research in a brainstorm-solutions workspace. Reads all context, executes queued prompts, and saves structured findings. Triggers on phrases like "deep research", "start researching", "run the research", "investigate this blocker".
---

# Deep Research

Execute the research workflow inside a brainstorm-solutions workspace. This skill assumes you are already inside a research workspace created by the `brainstorm` skill.

## Pre-flight

1. Confirm this is a research workspace — check for `context/`, `prompts/`, `outputs/` directories and a `research-brief.md`.
2. Read all context files:
   - `context/from-human/research-brief.md` — the blocker and what was tried
   - Everything in `context/from-history/` — prior compacted findings (if any)
   - Everything in `context/from-internet/` — any saved reference material

## Research execution

1. Check `prompts/queue/` for the next prompt to run. If empty, check `prompts/run/initial/` for unexecuted prompts.
2. Read the prompt file thoroughly.
3. Conduct research using all available tools:
   - **Web search** (Tavily, web fetch) for documentation, GitHub issues, forums, blog posts
   - **Context7** for library/framework documentation
   - **Reasoning** to synthesize findings
4. Save output to `outputs/individual/YYYY-MM-DD-<slug>.md` with:
   - `## Key Findings` section at the top
   - Detailed analysis in the body
   - `## Sources` section at the bottom with URLs
5. Move the executed prompt from `prompts/queue/` to `prompts/run/initial/` (or `subsequent/` if it builds on prior work).
6. If the findings suggest follow-up questions, create new prompt files in `prompts/queue/` for subsequent investigation.

## After research

1. Commit and push all new outputs and prompt movements.
2. Summarize findings to the user with:
   - Top 3 most promising approaches
   - Recommended next step
   - Any open questions that need further investigation
3. Ask if the user wants to:
   - Run another research iteration (deeper dive on a specific approach)
   - Compact findings into `context/from-history/`
   - Aggregate into a final report in `outputs/final/`
