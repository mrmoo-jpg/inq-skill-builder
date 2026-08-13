# Upstream Diff Report: tims-skill-creator-prime vs anthropics/skills skill-creator

- **Local fork:** `E:\paleo\skills buildin\tims-skill-creator-prime` (not modified by this research)
- **Upstream:** `https://github.com/anthropics/skills`, path `skills/skill-creator/`, cloned to
  `E:\paleo\skills buildin\research\upstream\skills` at commit `f17010c9bb483898c1d9c9f42dde2b3a98889434` (2026-08-07)
- **Method:** `diff -rq` for file inventory, then per-file `diff --strip-trailing-cr` (local files use CRLF, upstream LF). Changed-line counts below ignore line-ending differences.

## Headline finding

The fork is **current, not stale**: every shared file except `SKILL.md` and `LICENSE.txt` is byte-identical (modulo CRLF) to today's upstream HEAD. All of Tim's customization lives in (a) the rewritten/interleaved `SKILL.md`, and (b) two added reference files: `references/qa-framework.md` and `references/benchmark-setup.md`.

---

## A. VERBATIM / NEAR-VERBATIM ANTHROPIC

Safe to reuse under Apache 2.0 with license + copyright notice retained.

### Files identical to current upstream (0 changed lines; CRLF-only difference)

| File | Notes |
|---|---|
| `agents/analyzer.md` | identical |
| `agents/comparator.md` | identical |
| `agents/grader.md` | identical |
| `assets/eval_review.html` | identical |
| `eval-viewer/generate_review.py` | identical |
| `eval-viewer/viewer.html` | identical |
| `references/schemas.md` | identical |
| `scripts/__init__.py` | identical |
| `scripts/aggregate_benchmark.py` | identical |
| `scripts/generate_report.py` | identical |
| `scripts/improve_description.py` | identical |
| `scripts/package_skill.py` | identical |
| `scripts/quick_validate.py` | identical |
| `scripts/run_eval.py` | identical |
| `scripts/run_loop.py` | identical |
| `scripts/utils.py` | identical |

### LICENSE.txt — near-verbatim (1-line difference)

Both are Apache License 2.0. Upstream's appendix line reads `Copyright 2026 Anthropic, PBC.`; the local copy left the template placeholder `Copyright [yyyy] [name of copyright owner]`. A rebuild should use the upstream version with the Anthropic copyright line.

### SKILL.md sections carried over from upstream (verbatim or near-verbatim)

Tim's SKILL.md retains the bulk of upstream's SKILL.md body. These sections match upstream essentially word-for-word (local line numbers first):

- `## Communicating with the user` (local L367 — note Tim also has a *modified* copy at L262; the L367 copy is the upstream one, lightly trimmed: upstream's default-case bullets differ slightly)
- `### Write the SKILL.md` (L393)
- `### Skill Writing Guide` (L402) including `## Report structure`, `## Commit message format`, `### Writing Style`, `### Test Cases`
- `## Running and evaluating test cases` (L494) — Steps 1–5, viewer description, feedback reading — verbatim
- `## Improving the skill` (L623) and `### The iteration loop` — verbatim
- `## Advanced: Blind comparison` (L656) — verbatim
- `## Description Optimization` (L664) — Steps 1–4, "How skill triggering works" — verbatim
- `### Package and Present (only if present_files tool is available)` (L739) — verbatim
- `## Claude.ai-specific instructions` (L751) — verbatim
- `## Cowork-Specific Instructions` (L776) — verbatim
- `## Reference files` (L790) — upstream text plus two added bullets for Tim's qa-framework.md and benchmark-setup.md

Sections upstream has that Tim **modified** (mixed provenance — treat the deltas as Tim's):
- YAML frontmatter (`name`, `description`) — fully rewritten by Tim
- Title + intro (upstream "# Skill Creator" + high-level loop bullets → replaced by Tim's pipeline framing)
- `### Capture Intent` / `### Interview and Research` (upstream) → reworked into `## Building the Skill (Post-Intake)` / `### Research & Context Gathering` with Living-Skill/Static-Skill branches spliced in
- Closing "Repeating the core loop" summary — upstream skeleton with Tim's extended-loop steps substituted

## B. TIM / HUBSPOT CUSTOM (clean-room surface — rewrite from intent, do not copy)

### Files with no upstream counterpart

| File | Intent (one line) |
|---|---|
| `references/qa-framework.md` (112 lines) | "HubSpot AI Tool QA Framework": standardized eval criteria for Claude Skills / Custom GPTs / Gemini Gems, mock-example selection, single-tool vs cross-platform modes, QA evaluation doc structure. |
| `references/benchmark-setup.md` (189 lines) | Workspace directory layout, `grading.json`/`evals.json` schemas, and exact script commands for running the benchmark pipeline inline on Claude.ai (derived from skill-creator's own infrastructure). |

### SKILL.md sections with no upstream counterpart (all Tim's; local line numbers)

| Section | Intent |
|---|---|
| Frontmatter `name`/`description` (L2–4) | Rebranded trigger description covering build/QA/benchmark/package/deploy pipeline and "when in doubt, trigger" guidance. |
| Intro (L7–14) | Frames the skill as Anthropic base + three additions: guided intake, Living Skills, self-QA enforcement. |
| `## Start Here: Guided Intake` + Phases 1–4 + `### Intake Summary Gate` (L18–71) | 13-question structured intake Q&A (problem, end user, triggers, output, UX design, context inventory, failure modes, QA owner) ending in a mandatory confirm-before-build summary block, with a skip-the-questions escape hatch. |
| `## Architecture Decision: Static vs. Living Skill` (L73–156) | Decide between bundling static context files vs Google-Drive-backed "Living Skills" fetched via `google_drive_fetch`; includes single-doc fetch pattern and hierarchical manifest-based lazy loading, plus constraints (internal-only, Docs not Sheets). |
| `## Self-QA Enforcement: Non-Negotiable Rules` (Rules 1–4, L158–224) | Bake QA gates into generated skills: visual inspection gate for visual output, per-item tracking tables, explicit checks for known failure modes, hard-STOP delivery step for both visual and text output. |
| `## HubSpot Naming Convention` (L226) | `[skill-name]-hubspot-media-skill` naming format; kebab-case for personal skills. |
| `## The Full Pipeline` (L240) | 9-step pipeline (intake → architecture → build → self-test → formal QA → fix → package → contributor QA → ship) plus mandatory `-v1/-v2` versioning rule; names contributor QA people (Carly/Jess). |
| `## Communicating with the user` (modified copy, L262) | Tim's tweaked variant of the upstream section (jargon-level guidance). |
| `## Formal QA Loop` (Steps 1–8, L275–365) | Full inline QA process: ingest skill, design 6–10 evals across required categories, adversarial-assertion requirement, issue-assertion parity, inline (no-subagent) Claude.ai execution, honest grading, benchmark + viewer commands, HubSpot QA evaluation doc, mandatory feedback-loop closure, versioned packaging and deliverables checklist. |
| Modifications to `## Building the Skill (Post-Intake)` / `### Research & Context Gathering` (L380–391) | Gate build behind completed intake; branch context-gathering by Living vs Static architecture; fetch Google Docs and convert to markdown for static bundling. |
| Modifications to closing loop summary (L800–823) | "Tim's extended loop" recap plus core self-QA reminder; adds his two reference files to the reference index. |

Clean-room note: the *intent* descriptions above are what a rebuild may implement; the verbatim text of these sections/files must not be copied.

## C. UPSTREAM EVOLUTION (what current upstream has that the fork lacks or replaced)

Because all code/agents/eval-viewer files match upstream HEAD exactly, there are **no newer upstream scripts or tooling** to pick up. The evolution delta is confined to SKILL.md content that Tim overwrote — a rebuild on current upstream should restore:

1. **Upstream frontmatter description** — concise trigger description tuned by Anthropic for the skill-creator use case (create/edit/eval/benchmark/optimize-description).
2. **Upstream intro + high-level loop** (upstream L8–30) — the flexible "figure out where the user is and jump in" framing, the explicit note that users can skip evals and "just vibe", and the pointer to run the description improver after the skill is done. Tim replaced this with a mandatory pipeline; upstream's flexibility guidance is worth restoring.
3. **`### Capture Intent`** (upstream L47–54) — extract-from-conversation-history guidance and the 4 intake questions, including the nuanced test-case question (objectively verifiable outputs benefit from tests; subjective ones often don't — suggest a default, let the user decide). Tim's 13-question intake supersedes this in his fork, but upstream's version is the current official baseline.
4. **`### Interview and Research`** (upstream L56–60) — recommends parallel research via subagents when available; Tim's version dropped the subagent parallelism (his Formal QA Loop explicitly says "No subagents", a Claude.ai-inline constraint).
5. **`## Communicating with the user`** (upstream L32) — canonical wording of the jargon-guidance section (Tim carries two divergent copies).
6. **Closing loop summary** (upstream L472–483) — the simpler canonical loop without HubSpot pipeline steps.

Repo-level context worth knowing for the rebuild: upstream now ships a `spec/` folder (skill spec), a `template/` skill scaffold, a `.claude-plugin/` marketplace manifest, and a `THIRD_PARTY_NOTICES.md` — none of which exist in Tim's standalone fork. The README states skill-creator is among the Apache 2.0 open-source skills (docx/pdf/pptx/xlsx are source-available, not open source — not relevant here).

## License and attribution

- **Upstream license:** Apache License 2.0 (`skills/skill-creator/LICENSE.txt`), copyright "2026 Anthropic, PBC." Confirmed open source per repo README.
- **Requirements when reusing upstream content (Apache 2.0 §4):** include a copy of the license; retain the Anthropic copyright notice; state significant changes made to any modified files; retain any NOTICE-file attributions (the repo has `THIRD_PARTY_NOTICES.md` at repo root covering third-party software — carry it if redistributing affected components). No copyleft; derivative works may be under different terms provided the above notices are kept.
- **Fix in any rebuild:** the local fork's LICENSE.txt still contains the unfilled `[yyyy] [name of copyright owner]` placeholder; use upstream's version.
