# The deep tier: a real evaluation, not just a test run

The light tier (`qa-light.md`) is a conversation: try it, look at it together,
fix the obvious miss. This tier is a measurement: a scored comparison of the
skill against no skill, across several realistic prompts, with a written
verdict for every assertion and a page you can actually look at afterward.
It costs more — time, and an environment that can run it — so it's opt-in,
offered per `qa-light.md` §7, never defaulted into. Read this file only once
that offer has been made and taken.

This tier doesn't reinvent evaluation machinery. It routes into the same
evaluation system vendored (copied in, with its open-source Apache license
kept intact) into this skill at
`references/schemas.md` (schemas), `agents/` (grading and comparison
agents), `scripts/` (aggregation), and `eval-viewer/` (the results page) —
the same system, in fact, that graded *this* skill during its own
development. What follows is how to point that machinery at the skill you
just built, in plain enough language that someone who's never run an eval
can decide whether it's worth asking for, and precise enough that a capable
environment can actually execute it.

## Contents
1. [What you get](#1-what-you-get)
2. [What it costs](#2-what-it-costs)
3. [Environment this needs](#3-environment-this-needs)
4. [Running it](#4-running-it)
5. [Reading the result](#5-reading-the-result)
6. [The closing statement](#6-the-closing-statement)
7. [On claude.ai, without Claude Code](#7-on-claudeai-without-claude-code)

## 1. What you get

Where the light tier is one or two runs judged by eye, this tier is a small
experiment: the same set of realistic prompts run twice each — once with the
built skill available, once without — with every assertion from step 1's
frame checked and cited against actual evidence, not impression. The output
is a benchmark (pass rates, with a delta between "with" and "without" that
shows what the skill is actually buying) and a viewer page where every run's
full transcript, output files, and per-assertion verdict are laid out
side by side. If two versions of the skill exist, this tier can also judge
them **blindly** — an agent that doesn't know which skill produced which
output picks the stronger one and explains why, then a second pass
"unblinds" the result to turn that verdict into concrete suggestions for the
weaker version.

None of that is available from a single conversational look — it's the
difference between "this seemed to work" and "here's the pass rate, here's
the exact evidence for each assertion, here's what changed in this version."

## 2. What it costs

Real time and real token spend: each realistic prompt runs twice (with-skill,
without-skill), each run gets read by a grading pass, and every extra run —
more prompts, more repeats per prompt to catch flakiness, a blind comparison
between two versions — multiplies that. A handful of prompts with a couple
of runs each is a matter of minutes and a modest token bill; the kind of
exhaustive sweep the deep tier *can* do (many prompts, several repeats,
blind comparison, benchmark analysis) is a bigger ask, not a default one.
Size the run to the stakes named in `qa-light.md` §7 — the reason this tier
was offered in the first place — rather than running everything just because
it's available.

## 3. Environment this needs

This tier is built on Claude Code subagents: separate, self-contained runs
of the skill and separate agents to grade and compare them, all spawned and
supervised from one working session. Read
`references/tactics/environments.md` before offering or running this tier —
its "What runs where" table is the source for the claim in this section, and
it carries the one open caveat: whether *this specific* subagent-orchestrated
pattern is available inside the claude.ai code-execution sandbox is inferred,
not confirmed, by that file's own account. Treat it as **Claude Code only**
until someone verifies otherwise, and see §7 for what to do instead when a
capable environment isn't available.

## 4. Running it

This is the sequence; each piece is documented in full where it lives; don't
duplicate its instructions here, follow them there.

1. **Have or write the eval set.** `evals.json` — schema in
   `references/schemas.md` — is a list of realistic prompts plus, for each,
   the assertions it must satisfy. Draw prompts and assertions straight from
   step 1's confirmed frame (the good-output criteria, the failure modes) the
   same way `qa-light.md` §2 does — a prompt invented from nothing tests
   nothing here either, and an assertion the user would shrug at if it failed
   is not one worth writing. If a light-tier pass already ran, its prompt(s)
   are a reasonable start; the deep tier's value is in adding more of them
   and repeats, not in switching to different ones.
2. **Run each eval twice — with the skill, without it.** For each prompt,
   launch two subagent runs: one with the built skill available, one without.
   Save each run's transcript and output files the way `agents/grader.md`
   and `eval-viewer/generate_review.py` expect them (a `transcript.md`, an
   `outputs/` directory, optionally `metrics.json`) — the grading and
   aggregation steps below read exactly that layout; `references/schemas.md`
   defines the JSON shapes that flow through it.
3. **Grade each run.** Read `agents/grader.md` and run it, as a subagent,
   once per run, passing it that run's assertions, transcript, and outputs.
   It writes a `grading.json` next to the run's outputs. The grader checks
   more than the named assertions — it also extracts and verifies claims the
   output makes on its own, and it's told to say so if an assertion turns
   out to be too weak to have caught a real failure; read what it flags.
4. **Aggregate into a benchmark.** Once every run has a `grading.json`, run:
   ```
   python skill/scripts/aggregate_benchmark.py <benchmark-dir> --skill-name <name> --skill-path <path>
   ```
   against the directory holding the `eval-N/with_skill/run-N/` and
   `eval-N/without_skill/run-N/` layout the previous step produced. This
   writes `benchmark.json` and a human-readable `benchmark.md` with pass-rate
   and timing deltas between the two configurations. (Verified directly:
   this command ran clean against this project's own iteration-1 workspace
   while writing this file.) The benchmark's `notes` field starts empty;
   optionally, the "Analyzing Benchmark Results" role in `agents/analyzer.md`
   can fill it with a written read of the numbers.
5. **Generate the results page.** Run:
   ```
   python skill/eval-viewer/generate_review.py <workspace-dir> --skill-name <name> --benchmark <benchmark-dir>/benchmark.json --static <output.html>
   ```
   to write a single self-contained HTML file with every run, transcript,
   and verdict embedded — hand that file to the user directly, or drop
   `--static` to serve it locally instead and open it live. (Also verified
   directly against this project's own workspace.)
6. **Optional: judge two versions blindly.** If there are two candidate
   versions of the built skill to choose between, read `agents/comparator.md`
   and run it as a subagent per prompt, passing it the two outputs unlabeled
   — it picks a winner on output quality and cites why. Then read
   `agents/analyzer.md` (the post-hoc analyzer half of that file) and run it
   to turn the blind verdict into concrete, prioritized suggestions for the
   weaker version. Both are optional; most single-skill deep-tier passes
   never need them.

## 5. Reading the result

The benchmark's headline number is the pass-rate delta between with-skill
and without-skill — how much better the assertions hold up when the skill is
available. Don't stop there: open the viewer and read a few of the actual
`grading.json` verdicts, especially any the grader flagged as weak
assertions or unverifiable claims. A 100% pass rate on assertions that would
also pass for a wrong answer says nothing; a grader's honest note that an
assertion needs tightening is worth more than the number next to it. This is
the same "report honestly" instinct SKILL.md asks for everywhere else —
apply it to the deep tier's own output, not just the built skill's.

## 6. The closing statement

Like the light tier, this one ends with a plain-language sentence the user
actually hears, but this version can cite the numbers instead of just
naming what was tried:

> "We ran [N] realistic prompts, each with and without the skill, [M] times
> each. With the skill it passed [X]% of what we checked; without it, [Y]%.
> [Name anything the grader flagged as a weak check or a claim worth
> watching.] That's real evidence for [the stakes that justified this tier]
> — not a guarantee, but a measured one."

Never let the number stand in for the caveat next to it — a high pass rate
next to a grader's note about a weak assertion still leaves the important
question open, and saying so is what makes this tier's evidence better
than a vibe, not just bigger.

## 7. On claude.ai, without Claude Code

If the person asking for this tier is in a plain claude.ai chat and not
Claude Code, don't attempt the subagent orchestration in §4 — per
`references/tactics/environments.md`, it's not confirmed to work there, and
a broken attempt is worse than an honest no. Explain instead, in one or two
sentences, what this tier would give them (§1) and what it would need
(§3) — a Claude Code session, or someone with one — and let them decide
whether that's worth arranging. That's the whole degraded path: an honest
description, not a silent failure and not a fake pass. The light tier
already ran, or is available now, either way.
