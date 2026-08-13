# Implementation Plan: INQ Skill Builder

Source of truth: `../SPEC.md` (reviewed draft). Research inputs in `../../research/`.

## Overview

Build the `inq-skill-builder` skill as a vertical-slice progression: first a working golden path (elicit → build → light QA → package) runnable zero-setup, then the eval-driven iteration loop, then the opt-in deep-QA tier and polish, then real-world dogfooding. Upstream Apache components are vendored, not rewritten; only the Tim/HubSpot concept layer is reimplemented from intent.

## Architecture Decisions

- **Rebase on upstream anthropics/skills (f17010c)** — all shared code is byte-identical to Tim's copy; take from `../../research/upstream/skills` with correct Apache attribution. (upstream-diff report)
- **Zip-only distribution**, skill folder at zip root, versioned filenames. `.skill` upload on Claude.ai unconfirmed. (platform report; SPEC OQ1)
- **Keep `inq-skill-builder`** despite gerund-naming guidance — brand wins; name is legal. (SPEC OQ3)
- **Manifest = `BUILD-MANIFEST.md` inside each built skill's folder** — portable, survives distribution. (SPEC OQ4)
- **Viewer restyle in v1, last in build order** — cosmetic, must not block function. (SPEC OQ5)
- **Evals before SKILL.md** — git history must show `evals/` predating `skill/SKILL.md`. (official guidance; SPEC success criteria)
- **SKILL.md is a router** — enduring principles + teaching voice only; elicitation technique, QA tiers, manifests, and all dated mechanics live in references. 300-line canary.

## Task List

### Phase 0: Foundation

#### Task 1: Vendor upstream components + licensing
**Description:** Copy upstream `scripts/`, `agents/`, `eval-viewer/`, `assets/eval_review.html`, `references/schemas.md` from the research clone into `skill/`. Write LICENSE.txt (Apache 2.0, "Copyright 2026 Anthropic, PBC"), note our modifications, carry THIRD_PARTY_NOTICES if present upstream. Add a top-of-repo NOTICE line in README-less form (comment in SKILL.md frontmatter `license: Apache-2.0` handled later).
**Acceptance:**
- [ ] Vendored files byte-identical to upstream clone (verify by hash)
- [ ] LICENSE.txt correct and complete; attribution states what we changed (nothing yet)
**Verification:** `git diff --no-index` against upstream clone shows only intended files; commit.
**Dependencies:** None. **Files:** `skill/scripts/*`, `skill/agents/*`, `skill/eval-viewer/*`, `skill/assets/*`, `skill/references/schemas.md`, `skill/LICENSE.txt` **Scope:** M (many files, zero creativity)

#### Task 2: Write the builder's own evals (before any skill content)
**Description:** Author ≥3 evals in `evals/evals.json` + per-eval metadata: (1) bare-chat golden path — no files, vague-ish idea, must reach a packaged zip conversationally; (2) in-repo build — seeded fixture repo with templates/examples/docs, must scan then ask grounded questions; (3) one-line vague idea — elicitation must adapt depth, not interrogate. Draft assertions targeting the spec's failure modes (interview bounce, scan overreach, missing manifest, missing teaching beats, jargon leakage).
**Acceptance:**
- [ ] ≥3 evals with prompts, expected outcomes, adversarial assertions
- [ ] Fixture repo for eval 2 committed under `evals/fixtures/`
**Verification:** Committed before any `skill/SKILL.md` exists (git history is the proof).
**Dependencies:** None. **Files:** `evals/evals.json`, `evals/fixtures/**` **Scope:** M

### Checkpoint A: Foundation
- [ ] Upstream vendored + licensed; evals committed first; human glance at eval prompts

### Phase 1: Golden-path slice

#### Task 3: Elicitation working session (with Owen)
**Description:** Interactive session to settle SPEC OQ6: which techniques make elicitation.md (laddering, critical-incident, playback, artifact-grounded probing), the "stop annoying me" heuristic (e.g., depth budget by idea-richness; user-invoked fast lane), and the artifact-scan boundary (scan narrowly / confirm aloud / redirect). Output: a short decisions doc.
**Acceptance:**
- [ ] `tasks/elicitation-decisions.md` records technique set, depth heuristic, scan boundary — signed off by Owen in conversation
**Verification:** Manual — Owen confirms.
**Dependencies:** None (can precede or parallel Tasks 1-2). **Files:** `tasks/elicitation-decisions.md` **Scope:** S

#### Task 4: `references/elicitation.md`
**Description:** Write the triangulated elicitation protocol from the decisions doc: artifact scan → grounded interview → playback gate, with graceful bare-chat degradation ("paste or upload an example"). Clean-room: reimplements intake *intent* only; no reuse of Tim's question list or wording.
**Acceptance:**
- [ ] Implements all Task 3 decisions; includes bare-chat degradation; playback/confirmation gate present
- [ ] No sentence-level overlap with Tim's Phase 1-4 intake text
**Verification:** Read-through against decisions doc; spot-diff against Tim's SKILL.md sections.
**Dependencies:** 3. **Files:** `skill/references/elicitation.md` **Scope:** S

#### Task 5: SKILL.md v0 — enduring core, teaching voice, router
**Description:** The creative heart. Frontmatter (portable fields only; placeholder description). Body: enduring principles (elicit before writing, progressive disclosure, test adversarially, honest decay, dogfood), the golden-path flow routing to references at each step, teaching voice with explain-why beats and the baseline-first demo, jargon gating, environment sensing (zero-setup default, power tier announcement).
**Acceptance:**
- [ ] ≤300 lines; routes to references rather than inlining mechanics
- [ ] Baseline-first beat + ≥5 explain-why beats present; no dated platform facts in body
- [ ] No sentence-level reuse of Tim's custom sections
**Verification:** Line count; read-through against spec's Code Style snippet register.
**Dependencies:** 2 (evals exist), 3. **Files:** `skill/SKILL.md` **Scope:** M

#### Task 6: `references/qa-light.md`
**Description:** The default conversational test-and-review loop: run the freshly built skill on 1-2 realistic prompts (subagent where available, inline otherwise), review output with the user, one revision pass, plain-language "what we tested and what we didn't" statement (the light-tier promise, SPEC OQ made concrete here).
**Acceptance:**
- [ ] Works with zero scripts; states its own limits honestly; defines when to offer the deep tier
**Verification:** Read-through; consistency with SKILL.md router.
**Dependencies:** 5. **Files:** `skill/references/qa-light.md` **Scope:** S

#### Task 7: Dated tactics files (×4)
**Description:** Write `tactics/packaging-and-install.md`, `tactics/frontmatter.md`, `tactics/triggering.md`, `tactics/environments.md` from the platform-capabilities report. Each opens with "Last verified: August 2026" + source URLs + the trust-reality-over-me line. Content: zip packaging/rename/versioning, install paths per platform, portable frontmatter allowlist + 200-char description target, description craft, what runs where.
**Acceptance:**
- [ ] All four stamped and sourced; UNCONFIRMED items from the report carried as explicitly unconfirmed
**Verification:** Cross-check each claim against `research/platform-capabilities.md`.
**Dependencies:** 1 (packaging claims reference vendored scripts). Parallelizable with 4-6. **Files:** `skill/references/tactics/*` **Scope:** M

#### Task 8: `references/manifest.md` + template
**Description:** Define `BUILD-MANIFEST.md` (written into every built skill's folder): what was built, date, tactics stamps used, trigger phrases tested, QA tier run. Plus the manual check-up flow: re-read manifest, compare against current tactics, deliver Keep/Improve/Update/Retire verdict in plain language.
**Acceptance:**
- [ ] Template + check-up flow complete; K/I/U/R vocabulary used; v2 Gardener hooks noted but not built
**Verification:** Dry-run the check-up flow against a hand-written sample manifest.
**Dependencies:** 5. **Files:** `skill/references/manifest.md` **Scope:** S

### Checkpoint B: Golden path exists
- [ ] `python -m` quick_validate passes on `skill/`
- [ ] Manual walkthrough of eval 1's script by hand: every routed reference exists, no dead ends
- [ ] Human review of SKILL.md voice before the eval loop burns tokens on it

### Phase 2: Iterate

#### Task 9: Eval loop iteration-1
**Description:** Run all evals with-skill vs. without-skill via subagents into `workspace/iteration-1/`, grade against assertions, aggregate benchmark, generate the (stock) viewer.
**Acceptance:**
- [ ] All evals executed both arms; grading.json per run; benchmark.json + viewer HTML generated
**Verification:** Viewer opens; assertions graded with evidence.
**Dependencies:** Checkpoint B. **Files:** `workspace/iteration-1/**` (gitignored), grading/benchmark artifacts **Scope:** M

#### Task 10: Review + revise (iteration-2 if needed)
**Description:** Owen reviews outputs in the viewer; revise skill per feedback + failed assertions; re-run failing evals as iteration-2. Watch specifically for: interview length complaints, scan overreach, teaching beats feeling like lecture, line-count creep.
**Acceptance:**
- [ ] Every instruction-level failure fixed or explicitly deferred with reason; Owen signs off
**Verification:** Iteration-2 assertions pass; feedback addressed.
**Dependencies:** 9. **Files:** `skill/**` revisions **Scope:** M

### Checkpoint C: Core validated
- [ ] Evals pass; Owen approves the experience, not just the scores

### Phase 3: Deep tier + polish

#### Task 11: `references/qa-deep.md`
**Description:** Opt-in rigorous tier: routes into the vendored upstream eval machinery (eval sets, assertions, benchmark, viewer) with plain-language framing for non-engineers and honest environment requirements (works best in Claude Code; degraded paths on Claude.ai per tactics/environments.md).
**Acceptance:**
- [ ] A technical user can run the full loop from it; a non-technical user understands what they'd get and what it needs
**Verification:** Read-through + one smoke run of the routed commands.
**Dependencies:** 1, 6. **Files:** `skill/references/qa-deep.md` **Scope:** S

#### Task 12: INQ restyle of eval viewer + eval_review.html
**Description:** Apply INQ design tokens (paper `#d7c9ac`, ink, state-dependent accent pair, Roboto Flex/Mono, 4px grid, hairline rules, grain) to `eval-viewer/viewer.html` + `generate_review.py` template + `assets/eval_review.html`. Function unchanged; note as a modification in LICENSE attribution.
**Acceptance:**
- [ ] Viewer renders with INQ look in light check; all interactions (tabs, feedback, export) still work; contrast pairs used correctly per tokens.css comments
**Verification:** Generate a viewer from iteration workspace; click through; screenshot for the site.
**Dependencies:** 9 (needs a workspace to render). **Files:** `skill/eval-viewer/*`, `skill/assets/eval_review.html`, `skill/LICENSE.txt` **Scope:** M

#### Task 13: Description + triggering pass
**Description:** Draft the ≤200-char third-person description with concrete trigger terms; build a 20-query trigger eval set (should/shouldn't, near-misses per upstream method); run the vendored optimization loop from Claude Code; apply best result.
**Acceptance:**
- [ ] Final description ≤200 chars; trigger eval results recorded; Owen approves wording (it's brand copy too)
**Verification:** Optimization loop report; manual read.
**Dependencies:** 10. **Files:** `skill/SKILL.md` frontmatter, `evals/trigger-set.json` **Scope:** S

#### Task 14: Clean-room + license audit
**Description:** Systematic similarity pass of all authored content against Tim's custom sections and his two reference files (script-assisted n-gram overlap + manual read). Verify LICENSE/attribution/portable-frontmatter compliance.
**Acceptance:**
- [ ] No sentence-level overlap with Tim's custom layer; attribution complete; frontmatter uses portable fields only
**Verification:** Overlap script output + manual spot checks recorded in `tasks/cleanroom-audit.md`.
**Dependencies:** 4-8, 11-13 complete. **Files:** `tasks/cleanroom-audit.md` **Scope:** S

### Checkpoint D: Ship-shape
- [ ] Success-criteria sweep against SPEC (all but the manual/dogfood items)

### Phase 4: Package + real world

#### Task 15: Package + platform install tests
**Description:** Package `skill/` → `dist/inq-skill-builder-v1.zip`. Install to `~/.claude/skills/` (Claude Code) and verify trigger + golden path start. **Owen manually** uploads to Claude.ai (Settings > Capabilities > Skills), confirms trigger and an in-chat golden path through to a downloadable zip.
**Acceptance:**
- [ ] Validates + packages clean; Claude Code install triggers; Claude.ai upload accepted and golden path completes zero-setup
**Verification:** Manual on both platforms; findings logged (any surprises → tactics files updated).
**Dependencies:** 14. **Files:** `dist/*` **Scope:** S (plus manual)

#### Task 16: Dogfood build #1 (Owen, real skill, in-repo)
**Description:** Use the installed skill to build a real skill Owen actually wants, in a repo with genuine artifacts. Full path: scan, interview, build, light QA, manifest, package.
**Acceptance:**
- [ ] A working skill exists that Owen keeps using; BUILD-MANIFEST.md written; friction list captured
**Verification:** The built skill triggers and works; friction log reviewed.
**Dependencies:** 15. **Files:** external + `tasks/dogfood-log.md` **Scope:** M

#### Task 17: Dogfood build #2 (non-engineer) + fixes → v2
**Description:** A genuine non-engineer (a UX researcher contact) builds a skill on Claude.ai with no facilitator rescue. Observe, capture bounce points, fix instruction-level issues, package v2.
**Acceptance:**
- [ ] Session completes without rescue OR every rescue converted to a fix; v2 packaged
**Verification:** Session notes; fixes verified by re-running affected evals.
**Dependencies:** 16. **Files:** `skill/**`, `dist/inq-skill-builder-v2.zip` **Scope:** M

### Checkpoint E: Complete
- [ ] All SPEC success criteria checked off
- [ ] Ready for site packaging (out of scope here: download page + companion article)

## Risks and Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Four behaviors bloat SKILL.md (Tim's-fork failure mode) | High | 300-line canary at Checkpoint B; router architecture; teaching beats capped per step |
| Interview annoys users / bounce | High | Task 3 depth heuristic; eval 3 targets it; Task 17 observes a real non-engineer |
| Claude.ai behavior differs from docs (packaging, upload) | Med | Tactics carry UNCONFIRMED flags; Task 15 manual test is the gate; surprises flow back into tactics files |
| Clean-room slip (accidental phrasing reuse) | Med | Fresh authoring from decisions docs, never with Tim's file open; Task 14 audit |
| Upstream moves during build | Low | Vendored pin at f17010c; restamp tactics at Task 15 |
| Scan overreach feels invasive | Med | Task 3 boundary decision; eval 2 assertion; confirm-aloud rule in elicitation.md |

## Parallelization

- Tasks 1, 2, 3 are mutually independent — can run in parallel at start
- After Task 5: Tasks 6, 7, 8 parallelize (7 also only needs Task 1)
- Tasks 11 and 12 parallelize after Phase 2
- Sequential spine: 3 → 4 → 5 → Checkpoint B → 9 → 10 → 13 → 14 → 15 → 16 → 17

## Open Questions

- Task 3 must happen live with Owen — schedule it as the first working session
- Who the Task 17 non-engineer is (Owen to nominate)
- Whether v1 ships publicly before or after the companion article (site decision, not blocking)
