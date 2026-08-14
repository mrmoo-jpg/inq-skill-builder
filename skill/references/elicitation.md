# Elicitation: finding the episode, filling the frame, playing it back

A skill inherits whatever you believe about the work when you start writing
it. A wrong guess confirmed here costs one sentence to fix; the same wrong
guess discovered after the build costs a rebuild. Elicitation is how you get
grounded before you write anything — not a form to complete, but a small set
of moves you deploy against whatever gaps are actually open once you've
looked at what's already in front of you.

Read this file when you're about to build a skill and haven't yet confirmed
what it needs to do. It ends the moment the user confirms a filled frame;
what happens after that (building, testing, packaging) lives elsewhere.

## Contents
1. [Find the episode before you interview anyone](#1-find-the-episode-before-you-interview-anyone)
2. [Fill the frame, not a form](#2-fill-the-frame-not-a-form)
3. [The moves: critical-incident and laddering](#3-the-moves-critical-incident-and-laddering)
4. [Play it back before you build anything](#4-play-it-back-before-you-build-anything)
5. [Offer a better question — after, not before](#5-offer-a-better-question--after-not-before)

## 1. Find the episode before you interview anyone

A real episode — one concrete time the work actually happened — beats a
description of a process every time. Ask someone to describe their general
approach and you get a folk theory, tidied up and missing the exceptions
that are usually where the expertise actually lives. Ask them to walk you
through the last real instance and the details come back on their own.

So before you ask a single question, check whether an episode already
exists. Look in this order, and stop at the first one that has something:

**Source 1 — the conversation you're already in.** Sometimes this skill
gets invoked right after the user *did* the thing — they built a prompt
chain, wrote a doc, fixed a report, and now say some version of "turn that
into a skill." If so, the transcript above you *is* the episode. Don't
re-ask anything it already answers — re-eliciting a fact the user just gave
you reads as not having listened. Mine it for specifics (the corrections
they made, the rules they stated, the mistake they caught and fixed), play
those back, and only ask about what's genuinely still missing.

**Source 2 — an artifact.** Something that shows the work: a template, a
past output, a process doc, an example held up as good or bad. Where this
comes from depends entirely on where the conversation is happening:

- If there's a working directory or repo, go find it — see the scan
  protocol below.
- If there isn't — a plain chat, no files attached, nothing to scan — **ask
  the user to paste or upload one.** Treat this as the default shape of
  this step for most people who use this skill, not a fallback for when
  the good path isn't available. Most of the people building skills with
  this tool are working in a browser tab, not a repo. "Paste in one real
  example — a report you wrote, some notes, whatever you've got" is a
  completely ordinary way to start, and it beats guessing blind by exactly
  as much as a scanned artifact would. Only move past this step once you've
  actually asked and the user has confirmed there's truly nothing to share.

**Source 3 — the user's memory, as a last resort.** Only when neither of
the above turned up anything does it become time to ask someone to
reconstruct an episode from memory. This is where critical-incident
questioning (§3) fires. It's the least reliable source — memory smooths
things the way folk theories do — so treat it as what's left after the
better options are exhausted, not the default opener.

### The artifact scan protocol

When there's somewhere to look, scan it like desk research with a visible
notebook: quick, bounded, and narrated as you go.

- **Scope it tight.** Start exactly where the conversation lives — the
  working directory, an attached folder. Do a name-and-type pass first,
  then skim the top of at most ~10 plausible candidates: templates,
  examples, READMEs, style guides, prior outputs. Never crawl the whole
  tree. Never leave the project folder. Never open dotfiles or anything
  that looks like credentials.
- **Say what you looked at.** After scanning, name the specific files you
  found — not "I looked around," the actual list — and ask the user which
  ones matter and what you're missing. Nothing earns a place in the built
  skill that the user didn't see named first. This found-list is also your
  candidate set for whatever reference material the built skill will need.
- **Default to looking, ask before reading deep.** Listing and skimming
  obvious project documentation needs no permission. Ask first before
  opening anything bulky, anything outside the project folder, or anything
  that's *data* rather than *documentation* — a survey export is not the
  same kind of thing as a style guide, even if both are `.md` files in the
  same folder.
- **Flag participant data, then respect the call.** If the scan turns up
  what looks like raw participant material — interview transcripts,
  recordings, survey exports with names attached — say so in one line
  (consent someone gave for a research study doesn't automatically extend
  to a skill that redistributes patterns from it) and let the user decide
  whether and how to use it. This is a flag, not a refusal: researchers own
  their own ethics calls, and the skill's job is to make sure the call
  actually gets made, not to make it for them.

## 2. Fill the frame, not a form

Everything you're eliciting is going toward six things the skill will need
to know how to do the job without the user standing over its shoulder:

1. **Job** — what actually gets done
2. **Trigger** — what the user says or does to invoke it
3. **Good-output criteria** — what quality looks like when the user isn't there to judge it
4. **Failure modes** — what going wrong looks like
5. **Required context** — what Claude will need that it won't have by default
6. **Audience** — who consumes the output

That's the whole frame. Your job is to fill it, and a productive triage
step (§1) often fills most of it before you ask anything — a rich upfront
description, a mined conversation, or a well-scanned artifact set can leave
only one or two slots actually open. **Ask only about the slots that are
still empty or that something you found contradicts.** Don't ask about a
slot the template already answered just to be thorough.

A few operating rules:

- **Name the slot in the question.** "Asking because the skill needs to
  know what 'good' looks like when you're not the one judging it" tells the
  user why you're asking, not just what. This is also how the teaching
  voice shows up inside elicitation itself — the explanation and the
  question are the same sentence.
- **One or two questions per turn.** A turn with three or more questions in
  it stops being a conversation and starts being an intake form, no matter
  how reasonable each individual question is.
- **Soft budget: about six questions total**, across the whole exchange —
  and a stop rule that overrides the budget: the moment two consecutive
  answers add nothing you didn't already know, stop asking and go straight
  to playback (§4). Grinding past that point doesn't produce better
  answers, just diminishing patience.
- **Offer the fast lane at any point — and reach for it yourself on a thin
  request.** If the user says some version of "just build it," stop asking.
  But don't wait to be told: a one-line idea with almost nothing in it can
  technically leave every slot empty, and naively working through all six
  in order would mean the *least* informative request gets the *most*
  interrogation — exactly backwards. When there's this little to go on,
  ask at most the one or two questions that would change the build most,
  then fill the rest with labeled assumptions and move straight to
  playback. Match the effort in your response to the effort in the
  request; the questioning is always optional, the playback gate never is.
- **Never fall back to a fixed question list.** If you notice yourself
  reaching for something that resembles a standing checklist — "let's cover
  end user, then triggers, then output format, then failure modes, in
  order" — stop. That's a script, and a script asks about slots that are
  already full just to complete itself. Ask about what's actually open,
  in whatever order the conversation makes natural, and stop when the frame
  is full rather than when a list runs out.

## 3. The moves: critical-incident and laddering

Two techniques do the actual extraction work, deployed only where §1 and §2
say there's a gap to close — never as a script to run start to finish.

**Critical-incident, the fallback spine.** "Walk me through the last time
you actually did this" — a real instance, not their general approach. Use
this only when Source 3 in §1 is where you landed, i.e. nothing in the
conversation or the artifacts supplied an episode already.

**Laddering, capped at two rungs.** When someone states a preference, a
rule, or a quality call — "it needs to feel punchy," "we always flag
outliers" — one "why" usually surfaces the actual criterion underneath the
stated rule, and the criterion is what's portable to a skill; the rule
by itself might not be. A second "why" can sometimes get you further. A
third starts to read as interrogation rather than curiosity — stop at two
regardless of whether you feel closer to bedrock.

**Tag what you hear as ends or means as you go.** Quality criteria, failure
modes, and audience are *ends* — capture these faithfully; they're the
user's real expertise and they'll transfer no matter how the skill ends up
structured. The steps they currently take to get there are *means* —
useful as a candidate shape for the skill, but not a requirement. A
person's current process reflects constraints a person has and a skill
doesn't (limited memory, no ability to run ten checks at once, a Tuesday
deadline). Don't encode "I do X then Y then Z" as gospel if the actual
requirement underneath is just "the output needs to be accurate and fast."

## 4. Play it back before you build anything

This is the one mandatory step. Every other move in this file is
situational — deployed where there's a gap, skipped where there isn't. This
one never is, and it survives the fast lane too: even a "just build it"
still gets a playback, just one made of labeled assumptions instead of
confirmed answers.

Before writing anything, summarize what you understood — the six slots from
§2, restated in plain language, not slot-by-slot jargon — and ask the user
to correct it. If you filled gaps with assumptions rather than answers,
label them as assumptions explicitly: "I'm assuming this is for your
manager, not your team — tell me if that's wrong."

Why this matters enough to be the one non-negotiable step: the skill will
encode whatever you believe right now. A wrong guess caught here costs one
correction; the same wrong guess discovered in the finished skill costs a
rebuild. This is the same reason researchers play back interview findings
to the people they interviewed rather than writing up notes straight to a
report — the person who lived the episode is the cheapest and most reliable
check you'll ever get on whether you understood it.

Wait for the user to actually engage with the playback — a confirmation, a
correction, anything — before moving on. A summary you deliver and then
build past without a response isn't a gate, it's a monologue.

## 5. Offer a better question — after, not before

Once the playback in §4 is confirmed — never before — it's worth spending
one or two sentences on where the *to-be* might diverge from the *as-is*
you just confirmed: a step a skill could collapse that a human couldn't,
a Claude capability the current process doesn't use. You know the medium
better than the user does; they know the domain better than you do.
Expecting them to spec the innovation themselves wastes the one thing
you're actually good for.

Make it an *offer*, not an announcement. "The skill could enforce that bar
automatically — want that, or keep it the way you do it now?" invites a
choice; "I'm going to build it so it enforces that bar" quietly makes the
choice for them. The wording is the whole difference — the first leaves the
user in charge of their own process, the second takes a decision from them
under the cover of being helpful. If you catch yourself stating what the
skill *will* do differently rather than asking whether it *should*, you've
slipped from offering into deciding; reword it as a question.

Ground the suggestion in something specific you actually found or heard —
"your April report has verbatim quotes and confidence ratings that your
February one doesn't; the skill could enforce that bar every time" — not a
generic "have you considered automating more of this?" that would fit any
project regardless of what this one is.

Sequencing matters here more than the content does. Proposing improvements
before the playback is confirmed poisons the playback — the user is now
reacting to your redesign instead of correcting your understanding, and you
lose the chance to find out whether you understood the as-is at all.
Confirm first. Suggest second.

Treat a rejection as data, not a dead end. "No — it has to stay two
separate passes, legal reviews between them" isn't a missed opportunity,
it's a constraint you didn't have before, and it belongs back in the frame
(§2) same as anything else you learned. The point of asking isn't to win
the suggestion; it's to find out whether the constraint you assumed away
was actually load-bearing.
