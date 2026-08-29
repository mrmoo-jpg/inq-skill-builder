# Task 13 — Description candidates and scoring

Verification artifact for `tasks/plan.md` Task 13 ("Optimization loop report. Manual read.").
Owen: read this to confirm or swap the provisional pick in `skill/SKILL.md`.

## Why this is rubric-scored, not loop-measured

The vendored optimization loop (`skill/scripts/run_loop.py` → `run_eval.py` +
`improve_description.py`) could not be run live in this environment.

Smoke test: ran a 1-query eval set through
`python -m scripts.run_eval --eval-set <tmp> --skill-path . --num-workers 1 --timeout 60 --runs-per-query 1 --verbose`
from `skill/`. It failed with:

```
Warning: query failed: [WinError 2] The system cannot find the file specified
```

`claude` itself is on PATH and works directly in this shell (`claude --version`
returns `2.1.232`). The failure is specific to how `run_eval.py` spawns it:
`run_single_query()` calls `subprocess.Popen(["claude", ...])` inside a worker
process owned by `ProcessPoolExecutor`, and that worker can't resolve the
`claude` binary — most likely a Windows PATH-inheritance/shim issue between
the parent Python process and its pooled workers, not a problem with the CLI
install itself. This is separate from (and in addition to) the general
concern that the loop needs live model calls — the report couldn't even get
to the point of making one.

No attempt was made to patch `run_eval.py`'s subprocess handling — that's a
vendored file and out of scope for Task 13. `evals/trigger-set.json` is
written in the exact schema `run_eval.py` expects
(`[{"query": str, "should_trigger": bool}, ...]`), so it's ready to feed the
loop directly the moment it's runnable (non-Windows shell, or a fix to the
worker's PATH/binary resolution).

In place of measured trigger rates, candidates below were scored by manual,
query-by-query reasoning against all 20 entries in `evals/trigger-set.json`,
per the rubric in "Scoring method" below. This is reasoned, not measured —
treat the scores as directional, not as a substitute for an eventual live run.

## Trigger-set method

`evals/trigger-set.json` — 20 hand-written queries, 10 should-trigger / 10
should-not-trigger, built from `references/tactics/triggering.md` (what a
user would actually type) and `references/tactics/frontmatter.md` (the
200-char target the candidates below are held to).

- **Should-trigger (10):** cover the golden-path verbs (build, package,
  test, check up, improve) in realistic, sometimes typo'd phrasing — "skil"
  for "skill", "outta" for "out of", "u" for "you" — including one query
  ("turn what I know about tagging research interviews into something claude
  can just do automatically") that never uses the literal word "skill" at
  all, to test whether a candidate is understood semantically rather than
  matched on keyword.
- **Should-not-trigger (10):** near-misses that deliberately share surface
  vocabulary with the golden path rather than being obviously unrelated —
  job "skills" (career advice), personal "skills" (Excel), an RPG "skill
  build," packaging a non-Claude app, "checking up" on a project deadline,
  browsing the Claude skills marketplace (informational, not a build
  request), and building an "agent" (adjacent concept, different noun).

## Candidates

All three: third person, imperative voice (per `improve_description.py`'s
own framing tips, carried into `tactics/triggering.md`), state both what the
skill does and when to reach for it, ≤200 chars.

| | Description | Chars |
|---|---|---|
| A | Turns a work process into a tested, installable Claude skill: scans examples, interviews for gaps, builds and tests it, packages a zip. Use to build, improve, package, or check up on a skill. | 191 |
| B | Builds a working Claude skill from a process someone knows: scans real examples, asks what's missing, tests it, packages an installable zip. Use to create, improve, test, or check up on a skill. | 194 |
| **C (applied, provisional)** | Turns tacit know-how into a packaged Claude skill, tested on real examples before delivery as a versioned zip. Use to build, improve, QA, or check up on a Claude skill, not general job skills. | 192 |

## Scoring method

For each candidate, walked all 20 queries and judged, by reasoning about
what a Claude instance would infer from the description alone (no other
skill content visible, per how `available_skills` triggering actually
works), whether it would plausibly open the skill.

**True-trigger set (all three candidates):** effectively 10/10. All three
cover build/package/test/check-up/scan-examples explicitly enough that even
the no-literal-"skill"-word query (turning "what I know" into "something
claude can just do") is a plausible semantic match, strongest for C
("tacit know-how" language mirrors that query closely) and adequate for
A/B via their "process" + "builds/turns...into a skill" framing.

**Should-not-trigger set — where the candidates actually diverge:**

- Query "help me improve my excel skills" and "what's the best skill build
  for a rogue in this game" are the hardest negatives: they collide
  literally with the trigger verbs ("improve," "build") plus the bare noun
  "skill." A and B both end their "Use to..." clause on an **unqualified**
  "a skill" ("check up on a skill," "improve...a skill") — the single
  riskiest phrasing, since it has nothing distinguishing a Claude skill
  artifact from a job skill, an Excel skill, or a game character build.
- C pairs every trigger verb with "**Claude** skill" specifically, and adds
  an explicit disqualifier — "not general job skills" — which directly
  inoculates the most literal near-miss (the UX-research-job-skills query)
  and generally primes toward "this is about a specific software artifact,"
  reducing (not eliminating) risk on the Excel-skills and game-skill-build
  cases too.
- "Package my python app into a windows executable" and the Claude-skills-
  marketplace browsing query remain non-trivial false-trigger risk for
  **all three** candidates roughly equally — this is inherent to competing
  on short, generic verbs like "package" and "skill" without ballooning
  past the 200-char target, and isn't something any of the three wordings
  solves outright. Flagged as an acceptable residual risk rather than
  claimed as solved.

**Net judgment:** A and B are close to interchangeable in both recall and
precision profile. C scores best on precision against the two clearest
near-misses (job skills, personal-ability "skills") without any recall cost
on the true-trigger set, because it qualifies "skill" with "Claude" at every
mention and adds one explicit exclusion. That is why C was applied as the
provisional pick.

## Owen's decision

Confirm C, or swap in A or B — the frontmatter comment in `skill/SKILL.md`
points here. If the vendored loop becomes runnable later (different shell,
or a fix to `run_eval.py`'s subprocess spawning), re-run it against
`evals/trigger-set.json` with whichever description is current at that time
and treat these scores as superseded by the measured result.

## Decision (Owen, 2026-08-17)

Owen's read: B reads better, and readability is half the point — the
description is also human-facing brand copy on the skill picker and the
download page. C's "tacit know-how" and "not general job skills" tail cost
clarity for a small, unmeasured precision gain.

**Applied: B′** — B's spine with C's one precision trick (say "Claude skill"
in the closing clause too, so no trigger verb ends on a bare "skill");
"real" dropped to fit the budget. 196 chars, quoted in YAML because of the
colon.

> Builds a working Claude skill from a process someone knows: scans
> examples, asks what's missing, tests it, packages an installable zip.
> Use to create, improve, test, or check up on a Claude skill.

The PROVISIONAL frontmatter comment is removed; v1 re-cut from this text.
