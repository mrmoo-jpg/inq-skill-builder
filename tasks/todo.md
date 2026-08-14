# INQ Skill Builder — Task Checklist

Full task details, acceptance criteria, and dependencies: `plan.md`. Spec: `../SPEC.md`.

## Phase 0: Foundation
- [x] 1. Vendor upstream Apache components + write LICENSE/attribution
- [x] 2. Write the builder's own evals (≥3) + fixture repo — BEFORE any SKILL.md

### Checkpoint A
- [x] Vendored + licensed; evals committed first; human glance at eval prompts

## Phase 1: Golden-path slice
- [x] 3. Elicitation working session with Owen → `tasks/elicitation-decisions.md`
- [x] 4. `references/elicitation.md`
- [x] 5. SKILL.md v0 — enduring core, teaching voice, router (≤300 lines)
- [x] 6. `references/qa-light.md`
- [x] 7. Dated tactics files ×4 (stamped, sourced)
- [x] 8. `references/manifest.md` + BUILD-MANIFEST template + check-up flow

### Checkpoint B
- [x] quick_validate passes; no dead-end routes; human review of SKILL.md voice (approved; may iterate later)

## Phase 2: Iterate
- [x] 9. Eval loop iteration-1 (with/without, grade, benchmark, viewer) — with-skill 94.6% vs 55.5% baseline, delta +0.39
- [x] 10. Owen review + revise (iterations 2-3 + variance study; Checkpoint C approved)

### Checkpoint C
- [x] Evals pass; Owen approves the experience (with-skill ~95%; better-question beat 8/8 on re-measure)

## Phase 3: Deep tier + polish
- [x] 11. `references/qa-deep.md`
- [x] 12. INQ restyle of eval viewer + eval_review.html
- [ ] 13. Description + triggering pass (≤200 chars; trigger eval set; optimization loop)
- [ ] 14. Clean-room + license audit → `tasks/cleanroom-audit.md`

### Checkpoint D
- [ ] SPEC success-criteria sweep (non-manual items)

## Phase 4: Package + real world
- [ ] 15. Package v1 + install tests (Claude Code auto; Claude.ai manual by Owen)
- [ ] 16. Dogfood build #1 — Owen, real skill, in-repo
- [ ] 17. Dogfood build #2 — non-engineer on Claude.ai → fixes → v2

### Checkpoint E
- [ ] All SPEC success criteria met; ready for site packaging
