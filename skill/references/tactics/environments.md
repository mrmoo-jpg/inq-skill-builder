# Environments — Dated Tactics
Last verified: August 2026 · Sources: support.claude.com/12111783, support.claude.com/12512180, support.claude.com/12512198, code.claude.com/docs/en/skills, platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices, platform.claude.com/docs/en/build-with-claude/skills-guide
Expect this file to age. If reality disagrees with it, trust reality and tell the user.

## What runs where

| Capability | Claude.ai (chat, code execution on) | Claude Code | API (Skills API, raw) |
|---|---|---|---|
| Bundled scripts execute (Python/Node) | Yes — sandboxed filesystem + bash | Yes — local machine | Yes, but no runtime installs |
| Network access from sandbox | Yes | Yes (local machine's own network) | **No** |
| Install packages at runtime (npm/PyPI) | Yes | Yes (local environment) | **No** — dependencies must be pre-installed |
| Pull from GitHub at runtime | Yes | Yes | No |
| Produce a user-downloadable file | Yes, up to 30 MB/file | N/A (local filesystem) | N/A (caller handles output) |
| Requires explicit capability toggle | Yes — "Code execution and file creation" in Settings → Capabilities | No | No |
| Requires terminal / local setup | No | Yes (Claude Code itself is a terminal tool) | No terminal required, but the caller must build and host their own integration around it |

## What this means for this skill's tiers

| Tier | Environment assumption | Consequence |
|---|---|---|
| Golden path (elicit → build → light QA → package) | Claude.ai, zero setup, code execution enabled | Must never require a script the sandbox can't run or a package it can't install — this is the hard floor SPEC.md calls the "zero-setup golden path" |
| Deep QA tier (`qa-deep.md`) | Best on Claude Code — subprocess calls to `claude -p` (see `triggering.md`'s optimizer) probably aren't available inside the claude.ai sandbox itself* | Route non-Code users to a degraded read-only explanation of what they'd get, per SPEC.md Boundaries; never silently fail |
| Trigger-optimization loop | Claude Code only* | Same reasoning — nested `claude -p` subprocess calls are a Claude Code pattern, not a claude.ai sandbox one |

*\* Inference, not confirmed by the platform report — `research/platform-capabilities.md` never addresses CLI/subprocess availability in the claude.ai sandbox. The judgment rests on the claude.ai code-execution sandbox being unlikely to ship the Claude Code CLI binary, not on a sourced fact. Verify directly before stating this to a user as settled.*

## Access & admin prerequisites

| Surface | Who needs to do what |
|---|---|
| Claude.ai, individual (Free/Pro/Max) | User enables "Code execution and file creation" in their own Settings → Capabilities. No admin involved. |
| Claude.ai, Team/Enterprise | Org **owner** must first enable "Code execution and file creation" **and** "Skills" in Organization settings → Skills before any member can use or toggle skills. Admins can also provision org-wide skills centrally. |
| Claude Code, personal | None — drop the folder, it's picked up live |
| Claude Code, project | None — commit `.claude/skills/<name>/` to the repo |

## Claude.ai ↔ Claude Code sync

Skills enabled on claude.ai can sync into a Claude Code session at `~/.claude/skills/synced/` (controlled by `CLAUDE_CODE_SYNC_SKILLS`); the `/skills` menu labels these "claude.ai sync." A local project skill of the same name takes precedence over a synced copy. Inside a synced skill, `!`-prefixed dynamic-context commands and `@` file references are neutered outside their home surface — don't assume they work post-sync.

## Slash-command note

Claude Code's `.claude/commands/*.md` custom commands have been merged into the skills system; both mechanisms produce `/name` invocations. Not relevant to claude.ai distribution, noted here only because it affects how this skill might be invoked inside Claude Code during dogfooding (Tasks 16–17).
