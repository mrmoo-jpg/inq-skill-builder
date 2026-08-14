# The build manifest: recording a build, checking up on it later

A **build manifest** is a short record, written into the finished skill's own
folder as `BUILD-MANIFEST.md`, of what got built, from what, and how it was
tested — so that a "does this still work?" question later costs a glance
instead of a reconstruction. (That's the whole definition; gloss it that
plainly the first time you say "manifest" out loud to the user, the same
way you'd gloss "frontmatter" or "eval" anywhere else in a build.)

Skills age two ways: the platform's mechanics move (a field renames, a limit
changes), and the user's own process moves (the report format changes, the
team merges, the rule that used to hold stops holding). Neither kind of drift
announces itself. The manifest exists so a future check-up has something to
compare against instead of starting from nothing.

Read this file at two moments:

- **While finishing a build** (SKILL.md step 5) — to write the manifest.
- **When someone comes back asking about an existing skill** — "is this
  still good?", "should I update this?", or anything in that family — to run
  the check-up flow.

## Contents
1. [Writing BUILD-MANIFEST.md — the template](#1-writing-build-manifestmd--the-template)
2. [Filling it with zero extra effort](#2-filling-it-with-zero-extra-effort)
3. [The check-up flow: Keep / Improve / Update / Retire](#3-the-check-up-flow-keep--improve--update--retire)
4. [Worked example: a sample manifest and a dry-run check-up](#4-worked-example-a-sample-manifest-and-a-dry-run-check-up)
5. [Not built yet: the Gardener (v2)](#5-not-built-yet-the-gardener-v2)

## 1. Writing BUILD-MANIFEST.md — the template

Write this file, filled in, into the built skill's own folder — next to its
`SKILL.md`, not buried in a references subfolder. It travels with the skill
wherever it's copied or shared.

```markdown
# Build Manifest — <skill name>

**Built:** <YYYY-MM-DD>, with inq-skill-builder (version <N>, if known)
**Package:** <filename delivered, e.g. report-cleaner-v1.zip — the one field
you can't fill yet at step 5; add it the moment step 6 produces the zip>

## What it does
<One or two plain-language sentences — the job and who the output is for.>

## Built from
<The actual sources used, named specifically — e.g. "scanned
templates/report-template.md and examples/strong-2026-04.md; interviewed the
user for the missing failure-mode slot" or "mined this session's prior
conversation about the Friday export." Never write a generic line like
"reviewed provided materials" — name the files or say what was mined.>

## Good-output criteria
<Bullet list — what the user said "good" looks like, in their words.>

## Known failure modes it guards against
<Bullet list — what going wrong looks like, in their words.>

## Trigger phrases
<The phrases the description was written around, plus anything the user
actually typed that should keep working — e.g. "clean up my standup notes",
"format this for Slack".>

## Tested
**QA tier:** <Light or Deep>
<The plain-language tested/not-tested statement the QA step closed with —
copy it verbatim rather than re-summarizing it.>

## Tactics referenced at build time
| File | Last verified (at build time) |
|---|---|
| tactics/packaging-and-install.md | <stamp> |
| tactics/frontmatter.md | <stamp> |
| tactics/triggering.md | <stamp> |
| tactics/environments.md | <stamp> |
<Only list the files you actually read for this build.>

## Open assumptions
<Anything labeled as an assumption during playback that the user didn't
explicitly confirm. Write "None — every slot was confirmed" if true.>
```

## 2. Filling it with zero extra effort

Every field above is something you already have by the time you reach step
5 — the frame from elicitation, the tactics files you actually opened while
building, the closing statement from whichever QA tier you ran. Writing the
manifest is transcription, not a new interview.

The one field that's a genuine exception is **Package**: step 5 runs before
step 6 produces the zip, so there's no filename to record yet. Write
everything else now, then return and add that one line the moment step 6
delivers the file — not a new question for the user, just a second, shorter
visit to a file you already opened once.

If any other field seems to need information you don't have, that's a
signal you're missing something earlier in the flow — go back and get it
there, or write the field as honestly incomplete ("not tested" is a
legitimate value; a vague guess dressed up as an answer is not). **Never**
ask the user a fresh question just to fill a manifest field. The "Tested"
tier line should read exactly as delivered in step 4 — copy it, don't
paraphrase it.

## 3. The check-up flow: Keep / Improve / Update / Retire

When someone comes back asking whether an existing skill still holds up,
this is the flow. The four-verdict vocabulary — Keep, Improve, Update,
Retire — is adapted from a third-party skill-auditing pattern called Skill
Stocktake (see `research/landscape.md`); it earns its place here because a
plain verdict word is something a non-engineer can act on without reading a
diff.

1. **Locate the manifest.** Ask for the skill's folder, or for the
   `BUILD-MANIFEST.md` pasted in, if it isn't already in front of you. No
   manifest, no check-up — say so, and offer to build one now from whatever
   the user can tell you about the skill instead.
2. **Compare tactics stamps.** For every file listed under "Tactics
   referenced at build time," open the current version of that same tactics
   file and compare its "Last verified" stamp and content to what the
   manifest recorded. A newer stamp with materially different facts is
   evidence of drift; an unchanged stamp is evidence the mechanics probably
   still hold.
3. **Ask about the world, not the mechanics.** The manifest can tell you
   what the platform looked like at build time; only the user can tell you
   whether *their* process changed since — a new report format, a merged
   team, a rule that stopped applying. Ask this directly; it's the one thing
   in this flow you can't infer from files.
4. **Weigh it into exactly one verdict**, and give the one-line reason plus
   one concrete next action alongside it — a verdict with no reason is just
   a label:
   - **Keep** — nothing material moved; the skill is still doing its job as
     built. Next action: none needed.
   - **Improve** — the skill still works, but something cheap would raise
     it — an untested edge case, a criterion that's drifted slightly, a
     trigger phrase that's stopped being how the user actually asks for it.
     Next action: a small revision pass, not a rebuild.
   - **Update** — a tactics fact the skill depends on has moved (packaging,
     frontmatter, an environment assumption) since the build date. The
     skill's judgment is probably still sound; its mechanics need a refresh.
     Next action: reread the affected tactics file(s) and patch the
     specific mechanic, then re-stamp the manifest.
   - **Retire** — the underlying job or process has changed enough that the
     skill's core frame (elicitation.md's own six-slot frame, its §2) no longer
     matches reality. Next action: a fresh build, not a patch — treat it as
     a new elicitation, not a repair of the old one.
5. **Don't hedge the verdict to cover multiple threads.** If both a tactic
   and the underlying process have drifted, pick the more consequential
   single verdict (usually Retire subsumes Update) and fold the rest into
   the reason line, rather than handing back two verdicts at once. A
   researcher asking "is this still good?" wants one word to act on, with
   the nuance available if they want it — not a hedge.

## 4. Worked example: a sample manifest and a dry-run check-up

Here's a hand-written sample manifest, as if `friday-standup-recap` had been
built five months before this file's current tactics stamps (August 2026 —
see the tactics files' own headers).

```markdown
# Build Manifest — friday-standup-recap

**Built:** 2026-03-14, with inq-skill-builder
**Package:** friday-standup-recap-v1.zip

## What it does
Turns a Friday brain-dump of what got done that week into a Slack-formatted
standup update, in the user's usual tone.

## Built from
Mined this session's prior conversation, where the user pasted three past
updates and corrected two draft rewrites live.

## Good-output criteria
- Reads like the user wrote it, not like a summary of their week
- Leads with blockers if there are any that week
- Under 150 words

## Known failure modes it guards against
- Turning it into a formal status report instead of a casual update
- Burying a blocker in the middle instead of leading with it

## Trigger phrases
"clean up my standup notes", "turn this into my Friday update"

## Tested
**QA tier:** Light
Tried it on two of the user's real weeks, one with a blocker and one
without; the user confirmed both read like their own voice. Not tested:
a week with more than one blocker, or a very short one-line week.

## Tactics referenced at build time
| File | Last verified (at build time) |
|---|---|
| tactics/frontmatter.md | March 2026 |
| tactics/triggering.md | March 2026 |
| tactics/packaging-and-install.md | March 2026 |

## Open assumptions
None — every slot was confirmed.
```

Now the dry run. The user pastes the manifest above and asks, "is this
still worth keeping?"

- *Locate:* Manifest's in hand — no problem there.
- *Compare tactics stamps:* The manifest cites three tactics files, each
  "Last verified: March 2026." This skill's actual `tactics/frontmatter.md`,
  `tactics/triggering.md`, and `tactics/packaging-and-install.md` are all now
  stamped August 2026 — five months newer. That alone doesn't mean anything
  broke; it means it's time to actually check rather than assume. Reading
  August's `frontmatter.md` against what March's build would have used, say
  the portable-field allowlist and the 200-character description target are
  unchanged — no drift found there. But August's `packaging-and-install.md`
  carries a caveat (the `package_skill.py` folder-naming mismatch) that
  wasn't flagged the same way five months ago. Worth a look, not urgent.
- *Ask about the world:* "Has your Friday update process changed since
  March — different team, different format, anything?" Say the user answers
  no, same routine.
- *Weigh it:* Nothing about the *job* moved (skip Retire). One dependency —
  packaging — has a newly-documented wrinkle worth a look, but the criteria,
  tone, and triggers are all still exactly what the user confirmed. That's
  Update, not Improve: the judgment is sound, a mechanic needs a glance.

**What you'd actually say to the user:** "Keep using this one — nothing
about how you work has changed, and neither has most of the platform. One
thing's worth a five-minute check: how it gets packaged has a documented
quirk now that didn't exist in March. I'd call this an **Update**, not a
rebuild — want me to check the current packaging tactics file and repackage
it if needed?"

## 5. Not built yet: the Gardener (v2)

Everything above is manual: a check-up happens because someone asked for
one. A future version could watch tactics files for changes and proactively
flag manifests that might be affected, or re-run a skill's own QA on a
schedule without being asked — call it the Gardener. None of that exists in
this skill. If a user asks for it, say so plainly rather than half-building
it: v1's manifest is a record for a check-up you run together, not a
background process.
