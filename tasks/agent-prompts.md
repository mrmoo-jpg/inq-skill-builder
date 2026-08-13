# Copy-paste prompts for build agents

One prompt per task. Start every agent in `E:\paleo\skills buildin\inq-skill-builder\`. Prompts are deliberately short — the repo carries the detail; each adds only task-specific emphasis a cold agent might miss.

**Wave order** (respecting dependencies):
- **Wave 1 (parallel):** Tasks 1, 2 — and the Task 3 session with Owen (any capable context, not a build agent)
- **Wave 2 (after 3):** Task 4, then 5; Task 7 can run parallel from Wave 1's end
- **Wave 3 (after 5, parallel):** Tasks 6, 8 → then Checkpoint B review with Owen
- **Wave 4:** Task 9 → Owen reviews (Checkpoint C) → Task 10
- **Wave 5 (parallel):** Tasks 11, 12, 13 → Task 14 (fresh agent only)
- **Wave 6:** Task 15 → 16 → 17

If agents run concurrently in one working tree, tell each to commit only its own files, frequently. Worktrees are safer if you have them.

---

## Task 1 — Vendor upstream + licensing

> Read CLAUDE.md in this repo, then complete **Task 1** from tasks/plan.md.
>
> Emphasis: the upstream clone should already exist at `E:\paleo\skills buildin\research\upstream\skills` (outside this repo) — use it; only re-clone (pinned f17010c) if it's missing. Copy files byte-exact; verify by hash against the clone and record the hash check in your commit message. Get LICENSE.txt right per research/upstream-diff.md's license section — the predecessor got this wrong and we care about doing it properly. Check whether upstream carries THIRD_PARTY_NOTICES and bring it if so. Touch nothing else in `skill/`.

## Task 2 — The builder's own evals (before any skill content)

> Read CLAUDE.md in this repo, then complete **Task 2** from tasks/plan.md.
>
> Emphasis: these evals must be committed while `skill/SKILL.md` does not exist — that ordering is itself a success criterion. Design the three evals from SPEC.md's Testing Strategy and write adversarial assertions against the spec's named failure modes (interview bounce, scan overreach, missing manifest, missing teaching beats, jargon leakage). An assertion you're confident will pass is decorative, not adversarial. The eval-2 fixture repo should feel like a real small project (a few docs, a template, two example outputs of visibly different quality — invent a plausible research-team scenario). Use `skill/references/schemas.md`-compatible eval JSON if Task 1 has landed; otherwise match the schema described in research/upstream-diff.md's upstream notes and say so in the commit.

## Task 3 — Elicitation working session (WITH OWEN — not a build agent)

> Read CLAUDE.md, SPEC.md, and tasks/plan.md Task 3 in this repo. You are running a live working session with Owen (a senior UX researcher — talk to him as one) to settle three design decisions for the skill's elicitation protocol:
> 1. Which interview techniques make the cut (candidates: laddering, critical-incident, playback/member-checking, artifact-grounded probing — argue for a small set, not all of them)
> 2. The "stop annoying me" heuristic — how the interview scales depth to the richness of what the user already provided, and how a user fast-lanes it
> 3. The artifact-scan boundary — how narrowly to scan, how to confirm findings aloud, when to ask permission vs. proceed
>
> Bring proposals, not open questions — Owen reacts better to something to push against. When decisions land, write `tasks/elicitation-decisions.md` capturing each decision with its rationale, get Owen's explicit OK on the file's content, commit it, and check off Task 3 in tasks/todo.md.

## Task 4 — references/elicitation.md

> Read CLAUDE.md in this repo, then complete **Task 4** from tasks/plan.md. `tasks/elicitation-decisions.md` must exist — if it doesn't, stop and say so.
>
> Emphasis: this file teaches Claude to *run* the protocol, so write it as instructions with explain-the-why, in the register of SPEC.md's Code Style snippet. The bare-chat degradation path (no files to scan → ask for pasted/uploaded examples) is not an afterthought; it's the primary path for the primary audience. Honor the clean-room rule absolutely: work only from the decisions doc and SPEC.md.

## Task 5 — SKILL.md v0 (the creative core)

> Read CLAUDE.md in this repo, then complete **Task 5** from tasks/plan.md. Requires Tasks 2, 3 (and ideally 4) done.
>
> Emphasis: this is the highest-craft task in the project — take your time, draft, re-read with fresh eyes, cut. ≤300 lines is a hard budget: when tempted to explain a mechanism, route to a reference instead. Every platform fact belongs in tactics files, not here — if you write a number (a limit, a path, a field name) in SKILL.md, you've probably made a mistake. The teaching voice means brief why-explanations at decision moments and the baseline-first demo beat, not lecturing. Frontmatter: portable fields only; description is a placeholder (Task 13 owns it). Before committing, run the evals' assertions through your head against your draft — you wrote nothing the evals will grade yet, but the skill must *route* to everything they'll demand.

## Task 6 — references/qa-light.md

> Read CLAUDE.md in this repo, then complete **Task 6** from tasks/plan.md. Requires Task 5.
>
> Emphasis: this tier's promise must be honest and concrete — it ends with a plain-language statement of what was tested and what wasn't. Zero scripts, zero jargon-without-explanation. Define crisply when to offer the deep tier (and when not to bother the user with it). Keep it short; it's the tier most users will actually experience.

## Task 7 — Dated tactics files ×4

> Read CLAUDE.md in this repo, then complete **Task 7** from tasks/plan.md.
>
> Emphasis: every factual claim traces to research/platform-capabilities.md — carry its UNCONFIRMED flags forward explicitly rather than resolving them optimistically. Each file opens with the stamp block from SPEC.md's Code Style plus the "trust reality over me" line. These files are designed to age honestly; write them so a future update is a surgical re-stamp, not a rewrite (facts in tables/lists, not woven into prose).

## Task 8 — references/manifest.md + template

> Read CLAUDE.md in this repo, then complete **Task 8** from tasks/plan.md. Requires Task 5.
>
> Emphasis: the BUILD-MANIFEST.md template must be fillable by the skill during a build with zero user effort. The check-up flow delivers Keep/Improve/Update/Retire verdicts in plain language a researcher understands. Dry-run the check-up yourself against a hand-written sample manifest (put the sample in the file as an example — it doubles as documentation). Note v2 Gardener hooks in one short section; build none of it.

## Checkpoint B — review prep (before any eval runs)

> Read CLAUDE.md in this repo. Tasks 1–8 should be complete. Prepare the Checkpoint B review for Owen:
> 1. Run `quick_validate` on `skill/` (fix trivial failures, report anything structural)
> 2. Report SKILL.md line count against the 300 budget
> 3. Walk eval 1's golden path through the skill on paper: list every reference SKILL.md routes to and confirm each exists and covers what the router promises — flag dead ends
> 4. Produce a one-page summary: what exists, line counts, validation status, dead ends, and the 3 things most worth Owen's attention in the voice review
>
> Do not revise the skill's content — this is inspection, not editing.

## Task 9 — Eval loop iteration-1

> Read CLAUDE.md in this repo, then complete **Task 9** from tasks/plan.md. Checkpoint B must be passed.
>
> Emphasis: with-skill AND without-skill arms for every eval, spawned in the same turn so they finish together. Capture timing/tokens from task notifications as runs complete (that data isn't recoverable later). Grade honestly — "the skill kind of did it" is a fail. Generate the stock viewer (no restyle yet — that's Task 12) and tell Owen where the HTML is. Everything lands in `workspace/iteration-1/` (gitignored); commit only grading/benchmark JSON summaries if plan says so.

## Task 10 — Review + revise

> Read CLAUDE.md in this repo, then complete **Task 10** from tasks/plan.md. Owen has reviewed iteration-1 in the viewer — read his feedback (feedback.json in the workspace, plus anything he tells you directly).
>
> Emphasis: generalize from feedback rather than patching the specific eval; if you find yourself adding an ALWAYS/NEVER, reframe as explanation. Fix instruction-level failures; label anything unfixable-at-the-skill-level explicitly and move on. Re-run only failing evals into iteration-2. Guard the 300-line budget while revising — bloat-by-fix is how the predecessor died.

## Task 11 — references/qa-deep.md

> Read CLAUDE.md in this repo, then complete **Task 11** from tasks/plan.md.
>
> Emphasis: this routes to the vendored upstream machinery — do not duplicate its instructions, point into them, adding only the plain-language framing (what you get, what it costs, what environment it needs per tactics/environments.md). Smoke-run the commands you cite so none are broken pointers. Two audiences in one file: the non-engineer deciding whether to opt in, and the capable environment actually executing.

## Task 12 — INQ restyle of the eval viewer

> Read CLAUDE.md in this repo, then complete **Task 12** from tasks/plan.md.
>
> Emphasis: the design system lives at `E:\paleo\irrelevantnextquarter\src\styles\tokens.css` (read its comments — contrast pairs are state-dependent and the file documents its own accessibility math; use `--accent-on-ink` on dark, etc.). Fonts: Roboto Flex/Mono — self-contained fallback stacks are fine, don't add font files to the skill. Function unchanged: tabs, feedback capture, export, `--static` mode must all still work — test by generating a viewer from `workspace/iteration-1/` and clicking through. Record the modification in LICENSE.txt attribution ("modified: visual restyle of eval-viewer and eval_review.html").

## Task 13 — Description + triggering pass

> Read CLAUDE.md in this repo, then complete **Task 13** from tasks/plan.md.
>
> Emphasis: ≤200 characters, third person, what + when + concrete trigger terms (per tactics/frontmatter.md and tactics/triggering.md). Build the 20-query trigger set with genuinely tricky near-miss negatives (queries that share keywords but need something else), realistic phrasing with typos and context, per the upstream method. Run the vendored optimization loop from Claude Code. The final wording is brand copy — present 2-3 candidates with scores and get Owen's pick before committing.

## Task 14 — Clean-room + license audit (FRESH AGENT ONLY)

> You must be an agent that has authored no content in this repo. Read CLAUDE.md in `E:\paleo\skills buildin\inq-skill-builder\`, then complete **Task 14** from tasks/plan.md.
>
> You are the one agent permitted to read `E:\paleo\skills buildin\tims-skill-creator-prime\` — for comparison only. Compare all authored content (SKILL.md, references/ excluding schemas.md) against Tim's custom sections and his qa-framework.md + benchmark-setup.md (research/upstream-diff.md section B lists exactly what's his). Method: script an n-gram overlap check (5-grams and up), then manually read flagged passages for paraphrase-too-close judgment calls. Also audit: LICENSE completeness, frontmatter portable-field compliance, tactics stamps present. Write findings to `tasks/cleanroom-audit.md` — including "clean" verdicts, so the audit is evidence, not vibes. Flag anything borderline for rewrite rather than rationalizing it as fine.

## Task 15 — Package + install tests

> Read CLAUDE.md in this repo, then complete **Task 15** from tasks/plan.md. Task 14 must be clean.
>
> Emphasis: package with the vendored script, verify the zip's internal layout (skill folder at zip root — unzip and check, don't assume), version the filename `inq-skill-builder-v1.zip`. Install to `~/.claude/skills/inq-skill-builder/` and verify it triggers on a natural prompt in a fresh Claude Code session. Then STOP and hand Owen the manual half: upload to Claude.ai (Settings → Capabilities → Skills → upload ZIP), confirm trigger, run a golden path to a downloadable zip. Log his findings; any doc-vs-reality surprises go back into the tactics files with a fresh stamp.

## Task 16 — Dogfood build #1 (Owen driving, agent observing/logging)

> Read CLAUDE.md in this repo, then support **Task 16** from tasks/plan.md. Owen will use the installed skill to build a real skill in one of his repos. Your job afterward: collect the friction log (`tasks/dogfood-log.md`) — where the interview dragged, where the scan over/under-reached, whether the manifest got written, whether teaching beats landed or lectured. Verify the built skill actually triggers and works. Propose fixes but change nothing without Owen's OK — fixes belong in the Task 17 revision pass.

## Task 17 — Dogfood build #2 (non-engineer) + fixes → v2

> Read CLAUDE.md in this repo, then complete **Task 17** from tasks/plan.md. A non-engineer will build a skill on Claude.ai using v1, observed but not rescued.
>
> Emphasis: every moment a facilitator *wanted* to intervene is a finding, even if they held back. Convert findings + Task 16's friction log into instruction-level fixes; re-run affected evals to verify; guard the line budget; package `inq-skill-builder-v2.zip`. Then run the SPEC.md success-criteria sweep and report which boxes check — that's Checkpoint E, and the project's done-or-not answer.
