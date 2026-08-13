# INQ Skill Builder — agent orientation

You are building `inq-skill-builder`: a Claude skill that helps non-engineers (UX researchers, designers, data folks) turn tacit expertise into tested Claude skills. It is Owen's personal tool AND a free public download from the Irrelevant Next Quarter site.

## Read in this order
1. `SPEC.md` — what and why; boundaries; success criteria. The source of truth.
2. `tasks/plan.md` — 17 tasks, dependencies, acceptance criteria. Find your task here.
3. `tasks/todo.md` — current status. Check off what you complete; keep it current.
4. `research/` — three reports the spec is grounded in. Cite them, don't re-research:
   - `upstream-diff.md` — what is Anthropic's (reusable) vs Tim's (clean-room)
   - `platform-capabilities.md` — verified platform facts (claude.ai packaging, frontmatter allowlist, limits)
   - `landscape.md` — competitive scan; attributed ideas we adopted

## ⛔ Clean-room rule (the one non-negotiable)
This skill re-implements *concepts* from a HubSpot-built predecessor whose text we may not reuse.
- **Never open** `E:\paleo\skills buildin\tims-skill-creator-prime\` while authoring content. Not for "inspiration", not to "check something". Everything you need from it is already distilled into SPEC.md and research/upstream-diff.md.
- Exception: Task 14 (clean-room audit) reads it — for comparison only, after all authoring is done, ideally by an agent that authored nothing.
- Upstream Anthropic files are different: Apache 2.0, reusable verbatim WITH attribution. Vendor them from `E:\paleo\skills buildin\research\upstream\skills` (outside this repo; if missing, `git clone --depth 1 https://github.com/anthropics/skills`, pin f17010c).

## Tasks that require Owen (do not fake these)
- **Task 3** — live elicitation working session. Its output (`tasks/elicitation-decisions.md`) gates Tasks 4 and 5. If it doesn't exist yet, STOP and ask rather than inventing the decisions.
- **Checkpoints B and C** — human review of voice and eval outputs.
- **Task 13** — final description wording is brand copy; Owen approves.
- **Task 15** — Claude.ai upload test is manual (Owen). Task 17 needs his nominated non-engineer.

## Conventions
- `skill/` is the distributable (zip root). Dev machinery stays outside it.
- SKILL.md ≤300 lines (hard ceiling 500). It routes to references; no platform mechanics in the body.
- Every `skill/references/tactics/*` file opens with `Last verified: <Month Year>` + source URLs.
- Frontmatter: portable fields only (`name, description, license, compatibility, metadata, allowed-tools`). Description ≤200 chars, third person.
- Writing register: imperative, explain-the-why over all-caps MUSTs, jargon gated behind plain language. See SPEC.md "Code Style" for the reference snippet.
- Commit per task, message referencing the task number. Never commit `workspace/` or `dist/` (gitignored).
- Windows environment; Python 3 for scripts; no Node dependency.

## Verify before you finish any task
Run the task's Verification block from `tasks/plan.md`, check the box in `tasks/todo.md`, and note deviations in your commit message. If your task changed a decision recorded in SPEC.md, update SPEC.md in the same commit — the spec is a living document.
