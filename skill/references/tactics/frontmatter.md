# Frontmatter — Dated Tactics
Last verified: August 2026 · Sources: code.claude.com/docs/en/skills, platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices, support.claude.com/12512198
Expect this file to age. If reality disagrees with it, trust reality and tell the user.

## The portable allowlist

Only these six fields are accepted by claude.ai uploads, the Skills API, and `package_skill.py`'s own validator. Anything else fails upload with: *"Unexpected key(s) in SKILL.md frontmatter... Allowed properties are: allowed-tools, compatibility, description, license, metadata, name."*

| Field | Required | Purpose |
|---|---|---|
| `name` | Yes | Skill identifier |
| `description` | Yes | The triggering mechanism — see `triggering.md` |
| `license` | No | e.g. `Apache-2.0` |
| `compatibility` | No | Declares supported surfaces/environments — format below is **⚠ UNCONFIRMED** |
| `metadata` | No | Free-form structured extras |
| `allowed-tools` | No | Tool restriction list |

**Claude-Code-only fields — do not ship these in a distributable skill:** `context: fork`, `disable-model-invocation`, `arguments` / `argument-hint`, `model`, dynamic `!`-prefixed context injection, `${CLAUDE_SKILL_DIR}` substitution. Claude Code accepts them locally; a claude.ai upload of a skill carrying any of them is rejected outright by the validator above. This project ships only the portable set (SPEC.md, Tech Stack).

## `name` rules

| Rule | Value |
|---|---|
| Max length | 64 characters |
| Character set | Lowercase letters, digits, hyphens only (kebab-case) |
| Banned | XML tags; the reserved words "anthropic" / "claude" anywhere in the name |
| Style guidance (not enforced) | Gerund form suggested (`processing-pdfs`) over noun form; avoid `helper` / `utils` |

This project keeps `inq-skill-builder` (noun form) as a deliberate brand deviation from the gerund-style guidance — decided in SPEC.md Open Question 3, not a platform requirement.

## `description` rules

| Source | Stated max | 
|---|---|
| Platform docs (platform.claude.com best-practices) | 1,024 characters |
| Claude.ai Help Center (create-custom-skills article) | 200 characters |
| `quick_validate.py`'s own enforced check | 1,024 characters |

**⚠ UNCONFIRMED — which limit the claude.ai uploader actually enforces today.** The two official sources disagree (200 vs 1,024) and the vendored validator only checks the looser 1,024 figure, so it will not catch a claude.ai-specific rejection. **Project target: ≤200 characters** — the stricter of the two, chosen to be safe for claude.ai distribution rather than to resolve the discrepancy (SPEC.md Tech Stack, Open Question 2). Other rules regardless of length limit: non-empty, no XML tags (`<` or `>`), third person (see `triggering.md`).

## `compatibility` field

**⚠ UNCONFIRMED — value format.** The field exists in the allowlist and is documented as declaring supported surfaces/environments, but the platform docs do not spell out its expected format (string? list? controlled vocabulary?) as of this pass. One concrete, verifiable fact independent of the research report: `quick_validate.py` (vendored, unmodified) enforces it as a plain string, max 500 characters, if present at all — that's an implementation detail of the vendored validator, not a confirmed platform spec. Until the format is confirmed, treat `compatibility` as optional and omit it rather than guess a structure that might fail silently elsewhere.

## Also relevant

`skill/scripts/quick_validate.py` (vendored, unmodified) is the fast local check for all of the above except the claude.ai-specific 200-char description question — see `packaging-and-install.md` for the command.
