# Spec: INQ Skill Builder

Status: DRAFT — awaiting Owen's review (spec-driven workflow, Phase 1 gate).
Inputs: idea one-pager (`irrelevantnextquarter/docs/ideas/inq-skill-builder.md`) and three research reports (`../research/upstream-diff.md`, `../research/platform-capabilities.md`, `../research/landscape.md`).

## Objective

Build **INQ Skill Builder** (`inq-skill-builder`): a Claude skill that helps a non-engineer — a UX researcher, designer, or data person — turn their tacit expertise into a tested, working Claude skill. Distributed free from Irrelevant Next Quarter; also Owen's personal skill-building system.

Four identity traits (settled in idea-refine; the spec makes them concrete, it does not relitigate them):

1. **Research-style elicitation via triangulation** — artifact scan first (in a repo/workspace), then a grounded knowledge-extraction interview (laddering, critical-incident, playback), then confirmation. Scan narrowly, confirm findings aloud, let the user redirect.
2. **Teach while building** — one-to-two-line "why" explanations at each craft decision; the user leaves with a skill *and* transferable skill-craft. Includes the baseline-first beat: show Claude attempting the task *without* the skill before building it (idea adapted from obra/superpowers, attributed in landscape report).
3. **Enduring vs. dated architecture** — SKILL.md carries only durable principles; every platform mechanic lives in a dated-tactics reference file with a visible "Last verified" stamp.
4. **Build manifests** — every skill built gets a manifest recording what/when/which tactics versions/trigger phrases tested. v1 includes a manual check-up flow using Keep/Improve/Update/Retire verdicts (vocabulary adapted from Skill Stocktake, attributed). Proactive Gardener is v2.

**Hard constraint:** the golden path (elicit → build → light QA → package → download) works in a plain Claude.ai chat on any plan, zero setup. Research confirmed this is possible: code execution + file downloads are available on all plans, scripts execute, packaged zips are downloadable (30 MB cap). Power tier (benchmark viewer, subagent runs, description optimization) lights up only where supported, announced in plain language.

**Users:** primary — non-engineer researcher on Claude.ai web; secondary — Owen and technical users on Claude Code. **Success looks like:** a researcher with no terminal experience completes a real skill end-to-end in one session and can explain two craft principles afterward; Owen prefers it over raw skill-creator for his own builds.

## Provenance & Licensing (from upstream-diff report)

- **Rebase on upstream, not on Tim's fork.** All shared code files (scripts/, agents/, eval-viewer/, assets/eval_review.html, references/schemas.md) are byte-identical to anthropics/skills HEAD (f17010c, 2026-08-07). Take them from the upstream clone at `../research/upstream/skills`.
- **Apache 2.0 obligations:** include LICENSE ("Copyright 2026 Anthropic, PBC"), state significant modifications, add THIRD_PARTY_NOTICES if upstream carries one.
- **Clean-room surface (reimplement from intent only, zero sentence-level reuse):** Tim's guided intake, Living Skills, Self-QA rules, pipeline, formal QA loop, `qa-framework.md`, `benchmark-setup.md`. The upstream-diff report is the authoritative inventory.
- **Also recover** upstream SKILL.md guidance Tim overwrote where useful (leaner intent capture, subagent parallelism, canonical communication section).

## Tech Stack

- Skill content: Markdown (SKILL.md + references), portable frontmatter only: `name, description, license, compatibility, metadata, allowed-tools`. No Claude-Code-only fields in the distributable.
- Scripts: Python 3 (matching upstream machinery). No Node dependency. Everything degrades gracefully where scripts can't run.
- Distribution: `.zip` with the skill folder at zip root (official upload format; `.skill` upload acceptance on Claude.ai is UNCONFIRMED — see Open Questions). Versioned filenames, never overwritten.
- Dev tooling (this repo, not shipped): git; upstream's `quick_validate.py` / `package_skill.py` for validation and packaging.

## Commands

Run from the repo root (`inq-skill-builder/`):

```
Validate:  python -m skill.scripts.quick_validate skill
Package:   python -m skill.scripts.package_skill skill dist/
           (then rename with version: inq-skill-builder-v<N>.zip)
Evals:     per-eval subagent runs into workspace/iteration-<N>/ (upstream loop; see Testing Strategy)
```

(Exact module paths to be confirmed when upstream scripts are vendored in — first build task.)

## Project Structure

```
inq-skill-builder/
├── SPEC.md                     ← this file
├── tasks/                      ← plan.md, todo.md (Phase 2/3 outputs)
├── skill/                      ← THE DISTRIBUTABLE (zip root = this folder)
│   ├── SKILL.md                ← enduring principles only; router to references
│   ├── LICENSE.txt             ← Apache 2.0, Anthropic copyright, modifications noted
│   ├── references/
│   │   ├── elicitation.md      ← artifact-scan protocol + interview techniques + playback
│   │   ├── qa-light.md         ← default conversational test-and-review loop
│   │   ├── qa-deep.md          ← opt-in eval/benchmark tier (routes to upstream machinery)
│   │   ├── manifest.md         ← build-manifest format + manual check-up flow (K/I/U/R verdicts)
│   │   └── tactics/            ← DATED, each file opens with "Last verified: <month year>"
│   │       ├── packaging-and-install.md   ← zip/upload/install paths per platform
│   │       ├── frontmatter.md             ← portable allowlist, name/description limits
│   │       ├── triggering.md              ← description craft + optimization loop pointer
│   │       └── environments.md            ← what works on claude.ai vs Claude Code vs API
│   ├── scripts/                ← upstream Apache (packaging, validation, eval loop)
│   ├── agents/                 ← upstream Apache (grader, comparator, analyzer)
│   ├── eval-viewer/            ← upstream Apache; INQ-restyled (deep-QA tier)
│   └── assets/
├── evals/                      ← evals for the builder itself (eval-first: written BEFORE SKILL.md)
├── workspace/                  ← eval run outputs (gitignored)
└── dist/                       ← versioned packaged zips (gitignored except notes)
```

## Code Style

**Markdown voice — the teaching register.** Imperative instructions to Claude, explain-the-why over all-caps MUSTs, jargon gated behind plain language. One snippet beats three paragraphs of description:

```markdown
## Step 3: Play it back

Before writing anything, summarize what you learned in plain language and ask
the user to correct it. Why this matters: the skill will encode whatever you
believe right now — a wrong guess confirmed here costs one sentence to fix;
the same wrong guess discovered after the build costs a rebuild. (This is the
same reason researchers play back interview findings to participants.)
```

**Dated-tactics files** open with a stamp and cite their source:

```markdown
# Packaging & Installing — Dated Tactics
Last verified: August 2026 · Sources: support.claude.com/12512198, code.claude.com/docs/en/skills
Expect this file to age. If reality disagrees with it, trust reality and tell the user.
```

**Conventions:** SKILL.md ≤300 lines (canary; official ceiling is 500). Reference files one level deep; TOC when >100 lines. Description: third person, what + when + concrete trigger terms, ≤200 characters (the stricter of the two documented limits). Python follows upstream style unmodified.

## Testing Strategy

Eval-first, per current official guidance: **≥3 evals written before SKILL.md** (they live in `evals/`), covering at minimum:
1. Bare-chat build (no files, no repo) — zero-setup golden path
2. In-repo build with artifacts present — scan → grounded interview
3. Vague one-line idea — elicitation depth adapts without annoying

Then:
- **Eval loop:** upstream's mechanism (with-skill vs. without-skill subagent runs, graded, viewer) run from Claude Code during development.
- **Dogfooding gate:** before any public release, build 2–3 *real* skills with it, at least one driven by a genuine non-engineer (assumption #1 from the one-pager); watch for interview bounce.
- **Manual platform test:** upload the packaged zip to Claude.ai via Settings, confirm it triggers, runs the golden path, and serves a downloadable zip from inside a plain chat. This is the zero-setup proof and cannot be automated.
- **Clean-room check:** similarity pass of the finished SKILL.md + references against Tim's custom sections (must be reimplementation-from-intent, no sentence-level overlap).

## Boundaries

- **Always:** retain Apache license/attribution when touching upstream files; stamp every tactics file; keep the golden path script-free-viable; gate jargon (explain "eval", "frontmatter" etc. on first use); write the build manifest for every skill built; version every packaged artifact.
- **Ask first:** adding frontmatter fields beyond the portable set; adding Python/JS dependencies; adding new bundled scripts; anything expanding v1 toward the proactive Gardener; changing the distributable's folder structure; publishing anywhere.
- **Never:** copy text from Tim's custom sections or HubSpot reference files; remove or thin upstream attribution; break the zero-setup golden path for a power feature; use "claude"/"anthropic" in the skill name; let SKILL.md exceed 500 lines (hard) or 300 (without discussion).

## Success Criteria

- [ ] ≥3 evals exist and predate SKILL.md authoring (git history shows it)
- [ ] SKILL.md ≤300 lines; description ≤200 chars, third person, concrete triggers
- [ ] `quick_validate` passes; packaged zip installs on Claude.ai (manual) and Claude Code (`~/.claude/skills/`)
- [ ] Golden path completes in a plain Claude.ai chat: artifact-less elicitation → build → light QA → downloadable versioned zip — zero terminal use
- [ ] In-repo run demonstrably scans artifacts and asks grounded questions about them (eval 2 transcript)
- [ ] Every tactics file carries a "Last verified" stamp + source links
- [ ] Each dogfooded build produces a build manifest; manual check-up flow returns K/I/U/R verdicts on a months-old manifest (simulated)
- [ ] Teaching beats present in transcripts (baseline-first demo + explain-why lines) — qualitative review
- [ ] Clean-room similarity check passes; LICENSE/attribution correct
- [ ] 2–3 dogfooded skills built, ≥1 by a non-engineer, without facilitator rescue

## Open Questions (for the poke-and-Q&A round)

1. **Ship `.zip` only, or dual `.zip` + `.skill`?** Claude.ai upload officially wants ZIP; `.skill` acceptance is unconfirmed. Recommend zip-only until confirmed.
2. **Description wording** — 200 chars is tight for "pushy" triggering; needs a dedicated drafting pass (the skill's own triggering-optimization loop can tune it later).
3. **Naming guidance tension** — official best practice now suggests gerund names ("building-skills"); we're keeping `inq-skill-builder` for brand. Accept the deviation? (Recommend yes; name is legal and brand matters more here.)
4. **Manifest location** — proposal: a `BUILD-MANIFEST.md` inside each built skill's folder (portable, survives distribution, invisible to frontmatter). Confirm or argue.
5. **Eval-viewer INQ restyle** — in v1 scope (it ships with qa-deep anyway) or defer to a fast-follow? Restyle is cosmetic; recommend in-scope but last in build order.
6. **Interview technique depth** — which techniques make the cut for elicitation.md and what's the "stop annoying me" heuristic. Needs a working session, not a spec line.
