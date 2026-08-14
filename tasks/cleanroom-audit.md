# Task 14 — Clean-room + license audit

Audited by a fresh agent that authored no content in this repo, with read access
to `tims-skill-creator-prime` for comparison only, per CLAUDE.md's Task 14
exception.

## Method

1. **Corpus assembly.** Concatenated all INQ-authored prose (`skill/SKILL.md`,
   `skill/references/elicitation.md`, `qa-light.md`, `qa-deep.md`,
   `manifest.md`, and all four `tactics/*.md` files — `references/schemas.md`
   excluded per instructions, since it's vendored Apache content, not
   authored) into `workspace/cleanroom/inq_authored.txt` (11,413 tokens).
2. Assembled Tim's custom-content corpus into `workspace/cleanroom/tim_custom.txt`
   (5,193 tokens): every SKILL.md section research/upstream-diff.md §B lists as
   his (frontmatter, intro, Guided Intake Phases 1–4 + Intake Summary Gate,
   Architecture Decision, Self-QA Enforcement Rules 1–4, HubSpot Naming
   Convention, The Full Pipeline, the modified "Communicating with the user"
   copy, the Formal QA Loop, the Building-the-Skill/Research & Context
   Gathering modifications, and the closing "Tim's extended loop" summary),
   plus the full text of his two reference-file additions,
   `references/qa-framework.md` and `references/benchmark-setup.md`.
3. Ran a word n-gram overlap script (`workspace/cleanroom/ngram_overlap.py`)
   at shingle sizes 5, 6, 7, 8, and 10 words (lowercased, markdown
   punctuation stripped) — every matching shingle at every size, not just a
   sampled subset.
4. Manually read every flagged match in context in both corpora
   (`workspace/cleanroom/show_context.py`, `show_tim_context.py`) and judged
   each for paraphrase-too-close risk.
5. Separately audited LICENSE.txt completeness, frontmatter portable-field
   compliance (including the PROVISIONAL-description YAML comment), and
   tactics-file stamps.

Working artifacts (gitignored, not committed): `workspace/cleanroom/inq_authored.txt`,
`workspace/cleanroom/tim_custom.txt`, `workspace/cleanroom/ngram_overlap.py`,
`workspace/cleanroom/show_context.py`, `workspace/cleanroom/show_tim_context.py`.

## A. N-gram overlap results — the numbers

| Shingle size | Matches |
|---|---|
| 5-word | 10 (4 distinct phrases, 2 repeated) |
| 6-word | 4 |
| 7-word | 1 |
| 8-word | 0 |
| 10-word | 0 |

Zero matches at 8 words or longer. **Clean** by the plan's "no sentence-level
overlap" bar as a threshold measure — nothing approaching a full sentence
survived. All four distinct phrases behind the 5–7-word matches, read in
context:

1. **"what the skill does and [when to...]"** — INQ (`SKILL.md`, twice):
   "say what the skill does and when to reach for it" / "state both what
   the skill does and when to use it." Tim (`qa-framework.md`/Formal QA Loop
   context): "note what the skill does and what it outputs." Different
   continuations, different clauses either side — this is generic
   skill-authoring boilerplate ("what it does and when to use it" is
   Anthropic's own upstream phrasing too, carried in the verbatim sections of
   Tim's SKILL.md). **Not a concern.**
2. **"after the skill is built [and...]"** — INQ (`qa-light.md`): "read it
   at step 4 of the main flow right after the skill is built and before you
   record." Tim: "run this after the skill is built and self tested."
   Ordinary transitional phrase, no distinctive content either side.
   **Not a concern.**
3. **"eval viewer generate review py [...]"** — both instances are literal
   references to the vendored script's own path/filename
   (`eval-viewer/generate_review.py`, Apache-licensed, listed in Task 14's
   scope as excluded reuse target since it's vendored code, not prose).
   Citing a filename you're both routing into isn't clean-room-relevant.
   **Not a concern.**
4. **"is not a clean bill of health"** (7-word exact match) — INQ
   (`qa-deep.md` §5, "Reading the result"): "...a high pass rate next to a
   grader's note about a weak assertion **is not a clean bill of health**,
   and saying so is what makes this tier's evidence better than a vibe."
   Tim (`benchmark-setup.md`, "Self-Grading Bias Warning"): "A '100%' with
   three UNTESTED issues **is not a clean bill of health**." Both uses land
   in the *same rhetorical slot* — warning that a high score doesn't mean
   the QA is trustworthy — which is exactly the kind of idea SPEC.md permits
   reimplementing from intent (the "report honestly" / anti-self-grading-bias
   theme research/upstream-diff.md attributes to Tim's benchmark-setup.md).
   The idiom itself ("clean bill of health") is common English, not
   Tim-original. But a 7-word exact match, in matching argumentative
   position, is the single result of this whole pass that's close enough to
   warrant a look rather than a wave-through.

## B. Manual judgment call — flag for rewrite

**Flagged, moderate severity:** `skill/references/qa-deep.md`, §5 "Reading
the result," the sentence containing "...is not a clean bill of health, and
saying so is what makes this tier's evidence better than a vibe, not just
bigger." This is not a sentence-level copy (the surrounding sentence differs
completely, and INQ's point — weak assertions undercutting a headline number
— is narrower than Tim's, which is about undocumented risks with no
assertion at all). It is, however, the one passage in this whole corpus where
a distinctive multi-word idiom lands in the same conceptual position as
Tim's, which is the exact shape of risk a clean-room audit exists to catch
even when it falls short of a hard violation. **Recommend rewording** to drop
the "clean bill of health" idiom — e.g. "a high pass rate next to a grader's
note about a weak assertion doesn't mean the run is trustworthy" — rather
than defending it as fine. This is a one-clause fix, not a section rewrite.

No other passage, at any n-gram size or on manual read-through of the full
corpus pair, showed sentence-level or near-sentence-level overlap. The two
corpora cover substantially overlapping *ground* (intake/elicitation, QA
tiers, description/triggering craft, packaging, a check-up/verdict flow) —
expected, since SPEC.md explicitly directs reimplementing Tim's *intent* —
but the prose is independently authored throughout, with the one exception
above.

## C. LICENSE.txt audit

**Structurally complete and correct:**
- Full, unmodified Apache License 2.0 text.
- Copyright line correctly reads "Copyright 2026 Anthropic, PBC." (upstream's
  correct value — not Tim's unfilled `[yyyy] [name of copyright owner]`
  placeholder, confirmed by direct diff against `tims-skill-creator-prime/LICENSE.txt`
  above).
- NOTICE section lists all 16 vendored files by exact path, with commit pin
  (`f17010c9bb...`) and source repo.
- A closing line disclaims the rest of the repo ("elicitation, SKILL.md,
  tactics, and other `inq-skill-builder`-authored content ... are original
  work and are not covered by the notice above") — correctly scopes the
  license notice.

**Task 12 amendment (viewer restyle) — adequate but with one gap:**
The NOTICE section documents the Task 12 changes to `eval-viewer/viewer.html`
and `assets/eval_review.html` in specific, itemized detail (design-token
substitution, corner-radius change, CDN-link removal, one JS-template color
literal → CSS variable) and correctly scopes them as "no markup, script, or
interaction logic changed." This satisfies Apache 2.0 §4(c) (retain
attribution notices) and is a genuinely good-faith, specific account — better
than a generic "modified" stamp.

**Gap, minor severity:** Apache 2.0 §4(b) requires modified files to
"carry prominent notices stating that You changed the files" — read literally,
that's a per-file requirement, not only a NOTICE-file requirement. I checked
the head of both modified files directly:

- `skill/eval-viewer/viewer.html` — opens with a comment describing the INQ
  design tokens and why they're transcribed rather than `@import`-ed, but
  does **not** state that this file has been modified from the Apache-2.0
  upstream or point to LICENSE.txt.
- `skill/assets/eval_review.html` — same: a design-tokens comment, no
  modification notice.

The LICENSE.txt NOTICE section is thorough enough that this is a paperwork
gap, not a substantive attribution failure — a reader auditing the repo would
find the change documented. But it doesn't fully satisfy §4(b)'s per-file
language. **Recommend:** add a one-line HTML comment near the top of both
files, e.g. `<!-- Modified from anthropics/skills skill-creator (Apache 2.0); see /LICENSE.txt for details -->`, next time either file is touched. Not
urgent enough to block shipping on its own, but worth closing before v1
ships publicly.

## D. Frontmatter portable-field compliance

`skill/SKILL.md` frontmatter:
```yaml
name: inq-skill-builder
# PROVISIONAL description — Owen must confirm or swap; candidates + scoring in tasks/description-candidates.md
description: Turns tacit know-how into a packaged Claude skill, tested on real examples before delivery as a versioned zip. Use to build, improve, QA, or check up on a Claude skill, not general job skills.
license: Apache-2.0
```

- **Fields used:** `name`, `description`, `license` — all three are in the
  portable allowlist (`tactics/frontmatter.md` §"The portable allowlist");
  no Claude-Code-only fields (`context: fork`, `disable-model-invocation`,
  `arguments`/`argument-hint`, `model`, `!`-prefixed injection) are present.
  **Compliant.**
- **`name`:** `inq-skill-builder`, 18 chars, kebab-case, no XML tags, no
  reserved words. Compliant with the rules table in `frontmatter.md` (the
  noun-vs-gerund deviation is a documented, deliberate brand call per SPEC.md
  OQ3, not a compliance issue).
- **`description`:** 192 characters — verified by direct count — under both
  this project's ≤200-char target and the platform's looser limits. Third
  person, no XML tags, states what the skill does and when to use it.
  Compliant.
- **PROVISIONAL comment — parse safety, verified directly:** loaded the raw
  frontmatter block through `yaml.safe_load()`. It parses to exactly three
  keys (`name`, `description`, `license`); the `#`-prefixed comment line is
  discarded by the YAML parser as expected — it does not leak into any field
  value, does not break parsing, and would not survive into a `.skill` ZIP
  as a fourth frontmatter key. **Verdict: parses safely.** It is, however, a
  real unresolved item, not just a formatting nicety: the description it
  flags as provisional is Task 13's deliverable, and Task 13's own
  acceptance criterion is "Owen approves wording (it's brand copy too)" —
  unaddressed, this comment is the one honest signal in the file that the
  frontmatter isn't final. It should be removed only once Task 13 is
  actually signed off, not simply because it parses cleanly today.

## E. Tactics-file stamps

All four required files present and stamped:

| File | Stamp present | Format |
|---|---|---|
| `skill/references/tactics/frontmatter.md` | Yes | "Last verified: August 2026 · Sources: ..." (3 URLs) |
| `skill/references/tactics/triggering.md` | Yes | "Last verified: August 2026 · Sources: ..." (4 sources) |
| `skill/references/tactics/packaging-and-install.md` | Yes | "Last verified: August 2026 · Sources: ..." (7 URLs) |
| `skill/references/tactics/environments.md` | Yes | "Last verified: August 2026 · Sources: ..." (6 URLs) |

All four also carry the required "Expect this file to age. If reality
disagrees with it, trust reality and tell the user." line immediately below
the stamp, and all four flag their own UNCONFIRMED items explicitly (⚠
markers) rather than presenting inference as fact. **Clean, all four.**

## Overall verdict

**Issues found, both minor/moderate, neither blocking, one flagged for
rewrite:**

1. **Rewrite recommended (moderate):** `qa-deep.md` §5's "is not a clean bill
   of health" sentence — the one passage close enough to Tim's phrasing, in a
   matching rhetorical position, to be worth rewording rather than
   rationalizing. See §B above for the exact clause and a suggested
   replacement.
2. **Gap, minor, not blocking:** the two Task-12-modified vendored HTML files
   don't carry their own inline modification notice, only the LICENSE.txt
   NOTICE section does. Recommend a one-line comment addition next touch.
3. **Not an issue, but tracked:** the frontmatter's PROVISIONAL description
   comment is honest and parses safely — it should simply not be forgotten
   before Task 15 packaging, since it's flagging a real open item (Task 13
   sign-off), not a false alarm.

Everything else audited — the full n-gram sweep down to 5-word shingles
across 11,413 words of authored prose against 5,193 words of Tim's custom
content, LICENSE.txt structure and attribution, frontmatter allowlist
compliance, and all four tactics stamps — came back clean, with the numbers
recorded above rather than asserted.

## Resolution (orchestrator, same day)

1. The flagged qa-deep.md idiom was reworded ("is not a clean bill of
   health" -> "still leaves the important question open") — fresh phrasing,
   no shared idiom, argumentative slot now expressed differently.
2. Both modified HTML files (eval-viewer/viewer.html,
   assets/eval_review.html) now carry a one-line inline modification
   comment referencing LICENSE.txt, satisfying Apache 2.0 s4(b) prominence.
3. The PROVISIONAL description comment is carried on the Task 15 checklist:
   Owen confirms the pick, and packaging strips the comment.

Both fixes land in the same commit as this audit.
