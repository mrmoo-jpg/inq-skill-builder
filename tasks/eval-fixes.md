# Eval suite fixes (post-metaprompt-review, pre-iteration-1)

**Status: all fixes applied.** evals updated to Task 3 decisions before any skill content was authored.

Reviewed 2026-08-14 against `tasks/elicitation-decisions.md` (which the evals predate — Task 2 ran parallel to Task 3; drift is expected, these fixes close it). Apply ALL fixes before Task 9 runs iteration-1. Executable by any agent: exact edits below. After applying, note the change in the commit as "evals updated to Task 3 decisions before any skill content was authored" — the eval-first ordering claim in SPEC survives because SKILL.md still doesn't exist.

## [x] F1 — Add the playback-gate assertion (evals 1, 2; tighten eval 3) — HIGHEST PRIORITY
The member-check is the one mandatory step in the elicitation design and currently no assertion tests it.

Add to eval 1 and eval 2 `expectations`:
> "Before any building starts, Claude delivers a visible playback — a structured summary of what it understood (the job, when it's used, what good output looks like, what can go wrong, what context it needs, who the output is for) — and the user gets a turn to confirm or correct it. Building that starts without a playback the user visibly responded to is a fail, no matter how good the built skill is."

Eval 3: replace its assertion 2 with an explicitly-named fast-lane playback:
> "Claude either asks at most 1-2 questions OR proposes proceeding on stated assumptions — and in both cases, before building, plays back what it intends to build as labeled assumptions the user can correct in one glance (e.g., 'I'm assuming these are for your manager, not your team — correct me if not'). Silent assumptions that first surface in the finished skill are a fail."

## [x] F2 — Add the better-question-beat assertion (eval 2)
The anchoring-bias counter from the working session has no assertion. Add to eval 2 `expectations`:
> "After the playback is confirmed — not before — Claude offers at least one concrete, opt-in improvement grounded in the artifacts (e.g., 'your February report lacks the evidence-and-confidence structure the April one has; the skill could enforce that bar automatically — want it to?'). Two fails hide here: never proposing any improvement to the as-is process, and proposing redesigns before the playback confirms the as-is."

Also append to eval 2 `expected_output`: "...and, after playback, proposes at least one artifact-grounded improvement rather than purely automating the current process."

## [x] F3 — Stop leading the witness (eval 2 prompt)
Current prompt instructs the found-list behavior that assertion 1 then grades, so a baseline run would pass it too.

Replace prompt with:
> "I run research for the Wayfinding team at Lumen Health. I want a skill that takes raw interview notes and turns them into an insight report in our format. Everything you need should be in this folder."

Update assertion 1's framing from "Before asking any questions, Claude names aloud..." to make clear the behavior must be unprompted:
> "Unprompted — the user never asked for a file inventory — Claude names aloud which specific files it looked at (template, the two example reports, process doc, taxonomy) before asking questions, rather than silently absorbing the folder or answering from a vague 'I looked around.'"

## [x] F4 — Tighten the per-turn question cap (eval 1, assertion 2)
Decisions doc says 1–2 questions per turn; the assertion currently allows 3. Replace with:
> "Claude asks at most 1-2 clarifying questions per turn; any single turn containing 3 or more questions is a fail, even if each question is individually reasonable."

## [x] F5 — Unify the jargon-gloss list (all evals)
One canonical list, identical wording in each eval's gloss assertion: **'eval', 'assertion', 'frontmatter', 'YAML', 'SKILL.md', 'manifest', 'trigger description'**. Keep each eval's existing fail phrasing; just make the term list the same everywhere.

## [x] F6 — Grading operationalization note (evals/README.md, not evals.json)
Append a "Grading notes for the subagent harness" section:
- "Delivered in the chat" = a versioned `.zip` exists in the run's outputs directory AND the transcript never instructs the user to run a command or open a terminal.
- "Playback the user visibly responded to" = the transcript contains the summary AND a subsequent user-role turn engaging with it (the harness's simulated user may simply confirm).
- Question-count assertions are graded per assistant turn as rendered in the transcript, counting interrogative sentences directed at the user.

## [x] F7 — New eval 4: mid-session invocation (the triage opener's source 1)
Owen's primary personal path — "we just built this, formalize it" — is untested. Repeatable eval beats one dogfood sample.

- Fixture: `evals/fixtures/eval-4-midsession/prior-conversation.md` — a scripted ~15-turn condensed transcript in which a user and Claude iteratively develop a working prompt-chain for turning weekly support tickets into a themes digest (include: 2 refinements the user made for tone, 1 explicit quality rule stated by the user, 1 failure they corrected). Follow the repo-root-relative path convention per evals/README.md.
- Prompt: "(The conversation in the attached file has just happened, in this same session.) ok this works great now — turn what we just built into a skill so i don't have to redo this every friday"
- Expectations:
  1. "Claude treats the prior conversation as the episode: it does NOT re-ask about anything the transcript already establishes (the tone rules, the quality rule, the corrected failure) — re-eliciting established facts is the core fail this eval exists to catch."
  2. "Claude asks at most 1-2 net-new gap questions (things genuinely absent from the transcript, e.g., who else will use this), or zero."
  3. "The playback arrives fast — within the first one or two assistant turns — and demonstrably reflects transcript specifics (names the tone refinements and the quality rule), proving the conversation was actually mined rather than generically summarized."
  4. "The built skill encodes the user's corrections from the transcript (the stated quality rule and corrected failure appear in the skill's instructions in some form)."
  5. Canonical jargon-gloss assertion (F5 list).
  6. "Golden path still completes: BUILD-MANIFEST.md produced (citing the conversation as the primary source), versioned zip delivered."

## [x] F8 — Fixture consistency repair (eval-2)
The strong report attributes the "tricked" verbatim to (P02, mobile); the transcript carrying that exact quote is P04's session. Fix: in `examples/insight-report-strong-2026-04-checkout-drop-off.md`, change the attribution to "(P04, mobile)" and adjust the same finding's "P04 and P05 independently closed the tab" to "P03 and P05" so P04 isn't double-counted contradictorily. Do not touch the transcript — its PII trap is calibrated correctly.

## Explicitly NOT fixing
- Eval 1's prompt announcing "I don't have a template or an example on hand" — mildly leading, but discovering absence isn't the behavior under test; the degradation response is. Leave it.
- evals/README.md path-resolution note — already correct; F6 appends to it, nothing changes.
