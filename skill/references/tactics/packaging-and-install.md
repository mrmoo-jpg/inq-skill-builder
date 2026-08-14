# Packaging & Installing — Dated Tactics
Last verified: August 2026 · Sources: support.claude.com/12512180, support.claude.com/12512198, support.claude.com/12111783, code.claude.com/docs/en/skills, platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices, platform.claude.com/docs/en/build-with-claude/skills-guide, github.com/anthropics/skills (skill-creator SKILL.md)
Expect this file to age. If reality disagrees with it, trust reality and tell the user.

## Packaging

| Fact | Detail |
|---|---|
| Vendored script | `scripts/package_skill.py` (upstream, unmodified) |
| What it produces | A ZIP archive named `<skill-folder-name>.skill` — a `.skill` file **is** a ZIP with a different extension |
| Project convention | Rename the output to `<name>-v<N>.zip` before handing it to the user. Ship `.zip`, not `.skill` — see the UNCONFIRMED note below. Never overwrite a previous version's file. |
| Root requirement | The skill folder itself must be the ZIP root — no wrapping subfolder |
| Runs zero-setup? | Yes — confirmed by Anthropic's own skill-creator: *"The `package_skill.py` script works anywhere with Python and a filesystem. On Claude.ai, you can run it and the user can download the resulting `.skill` file."* |

**⚠ UNCONFIRMED — `.skill` vs `.zip` at upload:** Official upload docs say only "ZIP file." Third-party reports say renaming `.skill` → `.zip` works. Whether claude.ai's uploader accepts a raw `.skill` extension is not confirmed by official docs. Safest default: always deliver `.zip`.

**⚠ UNCONFIRMED — exhaustiveness of downloadable file types:** The Claude.ai file-creation help article lists xlsx/pptx/docx/PDF/PNG/Python as example downloadable types and does not explicitly mention `.zip`. Treat this as a non-exhaustive example list (not a hard allowlist) — Anthropic's own skill-creator claims `.skill` (i.e. zip) output is downloadable in the same environment. If a user reports a download failing, that's new information; don't argue with it.

## Installing — by platform

| Platform | Path / mechanism | Prerequisite |
|---|---|---|
| Claude.ai (end user) | Settings → Customize → Skills → "+" → Create skill → Upload a skill (ZIP) | "Code execution and file creation" capability enabled (Settings → Capabilities). Skills available on Free, Pro, Max, Team, Enterprise. |
| Claude.ai (Team/Enterprise org) | Same, plus org-wide provisioning | Org owner must enable "Code execution and file creation" **and** "Skills" in Organization settings → Skills before members can use them |
| Claude Code — personal | Drop folder at `~/.claude/skills/<skill-name>/SKILL.md` | None. Applies across all projects; picked up live within a session, no restart |
| Claude Code — project | `.claude/skills/<skill-name>/SKILL.md` | None. Scoped to that project; can be committed to the repo |
| Claude Code — plugin/marketplace | `/plugin marketplace add <repo>` then `/plugin install <plugin>@<marketplace>` | Marketplace repo must be added first; plugin skills are namespaced `/plugin-name:skill-name` |
| Claude.ai → Claude Code sync | Skills enabled on claude.ai can sync to `~/.claude/skills/synced/` via `CLAUDE_CODE_SYNC_SKILLS` | A local project skill of the same name overrides the synced copy. `!` dynamic-context and `@` file references inside a synced skill are neutered outside claude.ai. |

ZIP upload validation (claude.ai): the folder must contain `SKILL.md`; folder name should match the skill name; invalid characters in name/description cause an upload error.

## Size limits

| Limit | Value | Source confidence |
|---|---|---|
| Claude.ai per-file upload/download cap | 30 MB | Confirmed (help article 12111783) |
| Skills API — total skill size | Under 30 MB **uncompressed** | Confirmed (platform skills-guide) |
| Claude.ai-specific skill-ZIP cap | Unpublished exact number; "limits exist" per Help Center | **⚠ UNCONFIRMED** exact figure — assume ≤30 MB and budget under it, not up to it |

## Do bundled scripts run on Claude.ai?

Yes. Skills execute inside a sandbox with filesystem + bash; bundled Python (or Node) scripts run there, and claude.ai's sandbox can install packages from npm and PyPI and pull from GitHub at runtime. This is **not** true of the API sandbox (no network access, no runtime installs — dependencies must be pre-installed there). If a skill build is destined for API use, do not assume install-on-the-fly.

## Commands (this project)

```
Validate:  python -m skill.scripts.quick_validate skill
Package:   python -m skill.scripts.package_skill skill dist/
           (produces dist/skill.skill → rename to dist/inq-skill-builder-v<N>.zip)
```

**Caveat verified by reading the vendored script (not the research report):** `package_skill.py` names both the output file and the ZIP's internal root folder after the *input directory's own name* — in this repo that directory is literally `skill/`, not `inq-skill-builder/`. Run it unmodified and the ZIP root folder inside will be `skill`, which won't match the `name:` in SKILL.md's frontmatter — the exact mismatch claude.ai's upload validation checks for ("folder name should match the skill name," §Installing above). At Task 15 (package + install tests), either package from a correctly-named copy of the folder (e.g. `inq-skill-builder/`) or confirm this has already been handled before trusting the output zip's internal layout.
