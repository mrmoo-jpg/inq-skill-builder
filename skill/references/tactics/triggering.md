# Triggering — Dated Tactics
Last verified: August 2026 · Sources: platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices, anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills, code.claude.com/docs/en/skills, this repo's `skill/scripts/improve_description.py`
Expect this file to age. If reality disagrees with it, trust reality and tell the user.

## What the description does

`description` is the entire triggering signal. Claude sees only `name` + `description` for every installed skill at startup (~100 tokens/skill, progressive-disclosure tier 1) and decides whether to open the skill from that alone — it may be choosing among 100+ candidates. Nothing else in the skill (body, references, scripts) influences that first decision.

## Drafting rules

| Rule | Detail |
|---|---|
| Person | Third person ("Use this for X" / "Helps with Y") — **not** first person ("I can help you..."). First person measurably harms discovery. |
| Content | State both *what the skill does* and *when to use it*, with concrete trigger terms a user would actually type |
| Length target (this project) | ≤200 characters — see `frontmatter.md` for the 200-vs-1,024 source discrepancy this target resolves |
| Focus | User intent and situation, not implementation details of how the skill works internally |
| Distinctiveness | The description competes with every other installed skill's description for the same decision — make it recognizably different, not generic |
| Voice (vendored optimizer's own framing) | Imperative — "Use this skill for..." rather than "this skill does..." |

## The optimization loop (vendored, opt-in, Task 13 in this project)

| Piece | Role |
|---|---|
| `evals/trigger-set.json` | A hand-written should-trigger / shouldn't-trigger / near-miss query set (≈20 queries per SPEC.md Task 13) |
| `skill/scripts/run_eval.py` | Runs the trigger set against the current description, scores pass/fail |
| `skill/scripts/improve_description.py` | Feeds failures (both missed triggers and false triggers) back to a fresh Claude call, asks for a structurally different rewrite — explicitly told not to just accumulate an ever-growing list of specific queries, to avoid overfitting |
| Hard limit enforced in code | 1,024 characters — the optimizer makes a second, "shorten this" call if a generated draft exceeds it |

This loop requires Claude Code (subprocess calls to `claude -p`, verified directly by reading the vendored `improve_description.py`); it is a Task 13 / dogfooding-tier activity, not part of the zero-setup claude.ai golden path.

**Inference, not confirmed by the platform report:** `research/platform-capabilities.md` never addresses CLI or subprocess availability inside the claude.ai sandbox. The judgment that this loop can't run there — because the claude.ai code-execution sandbox is very unlikely to ship the Claude Code CLI binary — is reasoned, not sourced. Treat it with the same caution as the report's own UNCONFIRMED items until someone actually tries it. See `qa-deep.md` for how a user reaches this tier and `environments.md` for the same caveat.

## Practical note carried from the optimizer's own prompt text

Real skills accumulate false triggers and missed triggers as separate failure modes — fixing one by narrowing the description often re-breaks the other. Treat trigger tuning as a small iterative loop with a scored stopping point (the eval set), not a single wordsmithing pass.
