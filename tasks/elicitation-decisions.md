# Elicitation Protocol — Design Decisions (Task 3)

Settled in a live working session with Owen, 2026-08-13. This doc is the input for Task 4 (`skill/references/elicitation.md`) and constrains Task 5. Written for the authoring agent: these are decisions, not options.

## Decision 1: Techniques — triage opener, three moves, one design beat

**The opening move is triage, not interview.** Before asking anything, determine whether a concrete episode of the work already exists. Episode sources, in rank order:
1. **The current conversation** — the user may invoke the skill mid-session, right after doing or building the thing (e.g. from plan mode, "now formalize this as a skill"). The transcript IS the episode; elicitation collapses to filling the frame from it plus playback and 1–2 gap questions.
2. **Scanned artifacts** that show the work (per Decision 3).
3. **The user's memory** — only here does critical-incident questioning fire.

**The moves:**
- **Critical-incident (fallback spine):** "walk me through the last time you actually did this" — a real episode, not the user's folk theory of their general process. Deploy only when sources 1–2 didn't supply an episode.
- **Laddering, capped at two rungs:** only at judgment moments (a stated preference, rule, or quality call), to extract the criterion underneath. Hard cap: two "why"s per thread — beyond that it reads as interrogation.
- **Playback / member-checking (mandatory gate):** one structured summary of the filled frame, corrected by the user before anything is built. Survives every path including the fast lane.

**Ends vs. means tagging.** Capture *ends* faithfully — quality criteria, failure modes, audience: that's the user's real expertise and it transfers to any process shape. Treat *means* (their current process steps) as candidates, not requirements — a human's workflow reflects human constraints a skill doesn't have.

**The better-question beat (after playback, opt-in).** Once the as-is is confirmed — never before — Claude briefly proposes where the to-be could diverge: steps a skill could collapse, Claude capabilities the user isn't exploiting. Rationale: the user knows the domain; Claude knows the medium; expecting users to spec the innovation is backwards. Rejections are signal — "no, it must stay separate passes because legal reviews pass two" is an elicited constraint; feed it back into the frame. Sequencing discipline: understand first, then challenge — proposing improvements before the member-check poisons the playback.

**Banned:** any fixed question list or numbered intake phases. Techniques are moves deployed against gaps, not a script to complete. (The predecessor's 13-question form is the named failure mode.)

## Decision 2: Depth heuristic — coverage, not count

The interview exists to fill a six-slot frame:
1. **Job** — what gets done
2. **Trigger** — what the user says/does to invoke it
3. **Good-output criteria** — what quality looks like without the user there to judge
4. **Failure modes** — what going wrong looks like
5. **Required context** — what Claude needs that it won't have by default
6. **Audience** — who consumes the output

Rules:
- **Ask only about empty or contradicted slots.** Rich upfront context, a mid-session invocation, or a productive artifact scan pre-fills slots and shrinks the interview automatically. No modes, no settings — depth scaling falls out of the frame.
- **Every question names its slot** in plain language ("asking because the skill needs to judge 'good' when you're not there"). This doubles as the teaching voice.
- **1–2 questions per turn. Soft budget: ~6 questions total.** Stop rule that outranks the budget: two consecutive answers adding nothing new → go to playback.
- **Fast lane:** at any point the user can say "just build it." Remaining slots get filled with *labeled assumptions*, played back for correction ("I'm assuming the audience is your stakeholders — correct me if not"). The playback gate survives; the questioning doesn't.

## Decision 3: Artifact scan — desk research with a visible notebook

- **Scope:** start where the conversation lives (working directory / uploaded files). Name-and-type pass first; skim the top of at most ~10 plausible candidates (templates, examples, READMEs, style guides, prior outputs). Never crawl the whole tree; never leave the project folder; never touch dotfiles or credentials.
- **Provenance rule:** nothing enters the built skill that the user didn't see cited. After scanning, present the found-list ("I found these — which matter, and what am I missing?") and let the user curate. The curated list doubles as the candidate set for the built skill's reference files.
- **Permission line:** proceed freely for listing and skimming obvious project documentation; ask first before reading anything bulky, anything outside the project folder, or anything that is *data* rather than *documentation*.
- **Participant data — aware, not paternalistic** (softened in session from a refuse-by-default proposal): when the scan hits what looks like raw participant material (transcripts, recordings, survey exports), the skill flags it and states the concern in one line — consent doesn't transfer, and a skill is a distribution vehicle — then respects the user's decision either way. Researchers own their ethics; the skill demonstrates awareness, not gatekeeping.

## Session provenance (for the record)

- Proposal 1 amended in session: triage opener added (user pushed on the mid-session/plan-mode invocation path); better-question beat added (user pushed on anchoring bias — elicitation of current practice risks encoding the status quo; the ends/means tag and post-playback design beat are the response).
- Proposal 2 accepted as proposed; budget of ~6 confirmed.
- Proposal 3 accepted with the participant-data default softened from refusal to flag-and-respect.
