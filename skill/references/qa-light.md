# Testing what you just built

A skill nobody has watched run is a promise, not a deliverable. This file is
the default way to close that gap: a short, conversational test-and-review
pass that needs no scripts, no separate tools, and no setup the user doesn't
already have — it works exactly as well in a plain Claude.ai chat as it does
in Claude Code. Read it at step 4 of the main flow, right after the skill is
built and before you record or package anything.

## Contents
1. [The loop](#1-the-loop)
2. [Picking the prompt(s)](#2-picking-the-prompts)
3. [Running it](#3-running-it)
4. [Reviewing together](#4-reviewing-together)
5. [One revision, not an iteration loop](#5-one-revision-not-an-iteration-loop)
6. [The closing statement — this tier's whole promise](#6-the-closing-statement--this-tiers-whole-promise)
7. [When to offer the deep tier — and when not to](#7-when-to-offer-the-deep-tier--and-when-not-to)

## 1. The loop

Five moves, run once: pick one or two realistic prompts grounded in what
elicitation surfaced → run the built skill on them → review the output with
the user against the criteria from the frame → revise once if something's
off → close with a plain statement of what was tested and what wasn't. If
you've already done steps 1–3 of the main flow, you already have everything
this loop needs — there's nothing new to set up.

## 2. Picking the prompt(s)

Draw the prompt straight from what elicitation surfaced — the artifact the
user shared, the critical incident they described, the good-output criteria
from the frame (`elicitation.md` §2). A prompt invented from nothing tests
nothing; a prompt built from the user's own example tests whether the skill
would actually have helped them last time they did this work.

One prompt is enough for a narrow skill. Use a second only when the frame
surfaced two meaningfully different situations the skill has to handle —
say, "the report with real quotes to work from" versus "the report where
nobody said anything usable." Testing more than two situations here is a
sign the skill needs the deep tier's harness (§7), not more of this one.

## 3. Running it

Run the skill on the chosen prompt(s) the way its real user will invoke it
— in a fresh conversation, not this one, so it can't quietly lean on context
it won't have later.

- **In Claude Code, use a subagent when you can** — a separate,
  self-contained Claude conversation spawned to do just this one run, the
  same mechanism the deep tier uses for its evaluations. It keeps the test
  honest (the skill can't see how it's "supposed" to answer) and leaves a
  transcript you and the user can both point at afterward.
- **In a plain Claude.ai chat, run it inline instead** — open a fresh chat
  if that's easiest, or just perform the task the skill describes as if you
  were encountering it new. There's no separate tool for this in a browser
  tab, and none is needed: a careful inline run is a real light-tier test,
  not a fallback for one.

## 4. Reviewing together

Walk the output past the good-output criteria and failure modes the frame
already named — the same bar the skill is supposed to clear, not a vague
"does this look right." Read it with the user, or have them look while you
do; a review nobody actually looks at isn't a review, it's a formality that
happens to produce a sentence about testing. Say plainly what worked and
what didn't. This is the cheap moment to catch a mediocre result — after
delivery is the expensive one.

## 5. One revision, not an iteration loop

Fix what the review turned up, once. This tier exists to catch the one
obvious miss, not to converge on perfection through repeated passes — that
kind of rigor is what the deep tier (§7) is built for. If one revision
doesn't fix it, treat that as information rather than a reason to keep
iterating here: either the problem needs more than a tweak, or this skill
has just earned the deeper tier.

## 6. The closing statement — this tier's whole promise

Every light-tier pass ends with one plain-language statement, said to the
user, not filed away somewhere they won't see it:

> "We tested this on [the prompt(s) you ran]. It handled [what it got
> right]. We didn't test [what's genuinely untested — a very different kind
> of input, edge cases, real scale, anyone besides you]. That's enough
> confidence for [personal, low-stakes use] — if [the condition that would
> change that], it's worth running the deeper check."

This sentence is the actual deliverable of this file. The loop above it
exists only so the sentence is true when you say it. Never round it up
("thoroughly tested") and never round it down out of excess caution
("basically untested" after a real review is false modesty, not honesty) —
say exactly what happened, in words the user would use themselves.

## 7. When to offer the deep tier — and when not to

Offer `qa-deep.md` when any of these is actually true, and name which one:

- **The output is high-stakes** — it feeds a decision with real
  consequences (money, a hire, a legal read) if the skill gets it wrong.
- **The skill will be used by people who never watched it get tested** —
  shared with a team, handed to a client, or published beyond the one
  light-tier pass you and the user just ran together.
- **The user asks whether it "really" works, or how reliable it is** — that
  question is the signal; answer it with what the deep tier would actually
  show, not with reassurance standing in for evidence.

Don't offer it otherwise. A personal skill the user will keep watching in
use, on stakes forgiving of an occasional miss, has already gotten the QA
that fits it — pushing the deeper tier onto it anyway is a cost with no
matching benefit, and it quietly teaches the wrong lesson: that a
light-tier pass wasn't real testing. It was, when the stakes matched it.
One sentence is enough to make the offer; if the user doesn't take it,
close and move on to recording the build.
