# Variance study — iteration-3 skill, multi-run

Purpose: iteration-3's single eval-2 run failed the better-question beat on sequencing. Before treating that as a real defect and editing the skill, measure whether it's systematic or run-to-run noise. Method: 8 fresh eval-2 runs + 3 fresh eval-1 runs against the unchanged iteration-3 skill (Sonnet executors, simulating a realistic user), scored on the behaviors that matter. n for the better-question beat = 8 fresh + iteration-2 (pass) + iteration-3 (fail) = 10.

## Headline

**The better-question beat is reliable — iteration-3 was an unlucky sample, not a defect.** A focused grader reading all 8 fresh transcripts in full scored the post-playback opt-in improvement offer **8/8 PASS**. Combined with iteration-2 (pass) and iteration-3 (fail): ~9/10 ≈ 90%+. No instruction fix is warranted; the earlier single-run failure was noise. (A crude regex pre-check suggested several failures, but it was grabbing pre-playback lead-in phrases and missing the genuine post-playback offers — the full-read grader is authoritative.)

## Results (fresh runs)

| Behavior | Rate | Note |
|---|---|---|
| Better-question beat offered after confirmed playback | 8/8 | The behavior iteration-3 missed; reliable on re-measurement |
| Versioned installable zip produced | 11/11 | Rock solid |
| Playback delivered before build | 8/8 | Grader-confirmed in all eval-2 runs |
| BUILD-MANIFEST.md written | 10/11 | run-5 (eval-2) skipped it entirely — no manifest in outputs or zip |
| Built skill free of real participant PII | 7/8 | run-1 used "Sarah's Visa" as the example *inside the redaction rule* — the real name baked into the shipped skill |
| Description ≤200 chars | where observed | 197 (eval-1 run-3), 192 (eval-2 run-6) |

## The two low-frequency wobbles worth noting

1. **Manifest occasionally skipped (~9%, 1/11).** run-5 completed the golden path — scan, elicit, build, test, package — but wrote no BUILD-MANIFEST.md. The manifest is the seed for the future check-up flow, so a silent skip quietly removes that. Low frequency, but it's a defined step being dropped.
2. **Real name as a redaction example (~12%, 1/8 eval-2).** The iteration-3 placeholder-example rule works most of the time, but run-1 reached for the real participant's name ("Sarah's Visa") precisely when writing the instruction that teaches stripping names — the one context where the real name is most salient to the model. Ironic and subtle; it bakes a real first name into a distributable.

Both are low-frequency and minor. Neither is a blocker. They are exactly the kind of tail-variance that light, one-time hardening addresses better than repeated single-run iteration — and that Phase 4 dogfooding (real multi-session use) is designed to surface in the wild.

## Recommendation

Accept Checkpoint C: the core behaviors are reliable and the delta over baseline is large and consistent. Optionally fold in two one-line hardening notes (always write the manifest; even the redaction example uses a placeholder name) since we now have concrete evidence these tail-cases occur — then stop measuring and let dogfooding be the real-world check. Do NOT keep single-run iterating on the better-question beat; it is not broken.
