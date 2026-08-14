# evals/

The builder's own eval suite (evaluating `inq-skill-builder` itself, not a
skill it builds). Written before `skill/SKILL.md` existed — see SPEC.md's
Testing Strategy.

**Schema note:** `evals.json` follows the `evals.json` shape documented in
`skill/references/schemas.md`, with one deliberate deviation. That schema
was written for skill-creator's own use, where `evals/` sits *inside* the
skill being tested and `files` paths are relative to that skill's root. This
repo keeps `evals/` as a sibling of `skill/` instead (per SPEC.md's project
structure — dev machinery stays outside the distributable), so `files`
paths here are relative to the **repo root**, e.g.
`evals/fixtures/eval-2-research-ops/...`. Anything that consumes
`evals.json` (the Task 9 eval-loop run) needs to resolve `files` against the
repo root, not `skill/`.

## Grading notes for the subagent harness

- "Delivered in the chat" = a versioned `.zip` exists in the run's outputs directory AND the transcript never instructs the user to run a command or open a terminal.
- "Playback the user visibly responded to" = the transcript contains the summary AND a subsequent user-role turn engaging with it (the harness's simulated user may simply confirm).
- Question-count assertions are graded per assistant turn as rendered in the transcript, counting interrogative sentences directed at the user.
