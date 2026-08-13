# Platform Capabilities Research: Skill Packaging, Installation, and Authoring Best Practices

Researched: 2026-08-13. Official Anthropic sources prioritized. Note: Anthropic docs have migrated domains — current canonical locations are **support.claude.com** (Claude.ai help), **platform.claude.com/docs** (API/platform docs, formerly docs.anthropic.com), and **code.claude.com/docs** (Claude Code). Older docs.anthropic.com URLs redirect.

---

## QUESTION A — Zero-setup packaging and installation on Claude.ai

### A1. Can a skill in a plain Claude.ai chat produce a downloadable .skill/.zip for the user? — YES (confirmed by Anthropic's own skill-creator)

- Anthropic's official `skill-creator` skill (in the official skills repo) states verbatim: **"The `package_skill.py` script works anywhere with Python and a filesystem. On Claude.ai, you can run it and the user can download the resulting `.skill` file"** and "After packaging, direct the user to the resulting `.skill` file path so they can install it."
  - Source: https://github.com/anthropics/skills — `skills/skill-creator/SKILL.md` (raw: https://raw.githubusercontent.com/anthropics/skills/main/skills/skill-creator/SKILL.md). Actively maintained repo (current as of mid-2026).
  - A `.skill` file is a ZIP archive with a different extension.
- Claude.ai's "Code execution and file creation" capability lets Claude create files in a sandboxed computing environment and offer them as downloads directly from the conversation. Available to **all plans including Free** (web, desktop, mobile). Max **30 MB per file for uploads and downloads**.
  - Source: https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude (current Help Center article).
  - Caveat: that support article only lists xlsx/pptx/docx/PDF/PNG/Python as examples; it does not explicitly list .zip as a downloadable type. However, the skill-creator quote above is Anthropic's own confirmation that archive files produced in the sandbox are user-downloadable. Treat "arbitrary file types downloadable" as CONFIRMED-BY-PRACTICE via skill-creator, with the support article's example list being non-exhaustive.
- The code-execution environment on **claude.ai can install packages from npm and PyPI and pull from GitHub** (unlike the API sandbox, which has no network access and no runtime installs).
  - Source: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices ("Package dependencies" section; current live doc).

**Net for zero-setup:** A distributed skill can instruct Claude (in a plain Claude.ai chat with code execution enabled) to assemble the skill folder, zip it, and hand the user a download link — no terminal required. Practical tip confirmed in the wild: downloads sometimes arrive with a `.skill` extension and Claude.ai's uploader is documented around ZIP, so the skill should either name the output `<name>.zip` directly or tell users they can rename `.skill` → `.zip`. (Whether the Claude.ai upload dialog accepts a raw `.skill` extension is **UNCONFIRMED** — official upload docs only say "ZIP file". Safest: ship .zip.)

### A2. How end users install a skill on Claude.ai today

- Path: **Customize > Skills** (settings), click **"+"** → "Create skill" → **"Upload a skill"** → upload a **ZIP file**.
  - Source: https://support.claude.com/en/articles/12512180-use-skills-in-claude (current Help Center).
- Prerequisite: **"Code execution and file creation"** must be enabled — Settings > Capabilities on Free/Pro/Max. Custom skills are available on **Free, Pro, Max, Team, and Enterprise**.
  - Sources: same article; https://support.claude.com/en/articles/12512198-how-to-create-custom-skills.
- **Org admin needed?** Only on Team/Enterprise: Owners must enable "Code execution and file creation" and "Skills" in Organization settings > Skills before members can use/toggle skills; admins can also provision org-wide skills. Individual (Free/Pro/Max) users need no admin.
  - Source: https://support.claude.com/en/articles/12512180 and 12512198.
- ZIP structure requirement: the **skill folder must be the ZIP root** (not nested in a subfolder); folder must contain `SKILL.md`; folder name should match the skill name; invalid characters in name/description cause upload errors.
  - Source: https://support.claude.com/en/articles/12512180 and 12512198.

### A3. How end users install on Claude Code

Source (current, canonical): https://code.claude.com/docs/en/skills

- **Personal:** drop the folder at `~/.claude/skills/<skill-name>/SKILL.md` — applies to all projects. Claude Code watches skill directories; changes are picked up live within a session (no restart needed for SKILL.md edits).
- **Project:** `.claude/skills/<skill-name>/SKILL.md` — this project only; can be committed to the repo.
- **Plugins/marketplace:** `/plugin marketplace add <repo>` then `/plugin install <plugin>@<marketplace>` (e.g., Anthropic's own `anthropic-agent-skills` marketplace from github.com/anthropics/skills). Plugin skills are namespaced `/plugin-name:skill-name`.
- **claude.ai sync:** skills enabled on claude.ai can sync into Claude Code at `~/.claude/skills/synced/` (via `CLAUDE_CODE_SYNC_SKILLS`); `/skills` menu labels them "claude.ai sync". Precedence: a local project skill overrides a synced claude.ai skill of the same name.
- Slash-command note: custom commands (`.claude/commands/*.md`) have been merged into skills; both create `/name` commands.

### A4. Current constraints (frontmatter, size, scripts)

- **Frontmatter accepted by claude.ai uploads / Skills API / package_skill.py** (exact allowlist, from Claude Code docs' compatibility table): `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools`. Anything else (e.g., Claude-Code-only fields like `argument-hint`, `context`, `disable-model-invocation`, `arguments`, `model`) causes an upload validation error: "Unexpected key(s) in SKILL.md frontmatter... Allowed properties are: allowed-tools, compatibility, description, license, metadata, name".
  - Source: https://code.claude.com/docs/en/skills ("Using skill frontmatter outside Claude Code"). Claude Code itself accepts a larger field set (including `context: fork`, `disable-model-invocation`, `allowed-tools`, `arguments`, dynamic-context `!` injection).
- **Name/description validation** (platform docs): `name` max 64 chars, lowercase letters/numbers/hyphens only, no XML tags, cannot contain reserved words "anthropic"/"claude"; `description` non-empty, max **1,024 chars**, no XML tags.
  - Source: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices.
  - DISCREPANCY: the Help Center create-custom-skills article says description max **200 chars** for claude.ai uploads (and name 64). Which limit the claude.ai uploader actually enforces today is **UNCONFIRMED** — to be safe for claude.ai distribution, keep the description ≤200 chars.
- **Size limits:** Claude.ai file upload/download cap is 30 MB per file (support 12111783). The Skills API states total skill size **under 30 MB uncompressed** (https://platform.claude.com/docs/en/build-with-claude/skills-guide). A claude.ai-specific skill-ZIP limit is acknowledged to exist but the number is not published in the Help Center ("ZIP file size limits exist") — exact claude.ai number **UNCONFIRMED**; assume ≤30 MB.
- **Do scripts/ execute on Claude.ai?** YES. Skills run in the code-execution sandbox with filesystem + bash; bundled Python/Node scripts execute, and claude.ai can install npm/PyPI packages at runtime. (API sandbox: no network, no runtime installs — dependencies must be pre-installed.)
  - Sources: https://support.claude.com/en/articles/12512198; https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices ("Runtime environment", "Package dependencies").

---

## QUESTION B — Current official skill-authoring best practices

Primary sources:
1. **Skill authoring best practices** — https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices (live platform doc; the current canonical authoring guide; substantially expanded since late 2025)
2. **Engineering blog: "Equipping agents for the real world with Agent Skills"** — https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills (published 2025-10-16; conceptual origin of the guidance)
3. **Claude Code skills doc** — https://code.claude.com/docs/en/skills (live; Claude-Code-specific extensions)
4. **anthropics/skills repo** — https://github.com/anthropics/skills (spec in `./spec`, template in `./template`, official examples; skill-creator)

### What current guidance emphasizes

- **Conciseness above all**: "The context window is a public good." Default assumption: Claude is already very smart — only add context it doesn't have; challenge every paragraph's token cost.
- **Description = the triggering mechanism**: one description field, max 1,024 chars (platform), must state both *what the skill does* and *when to use it*, with concrete trigger terms; **write in third person** (descriptions are injected into the system prompt; "I can help you..." harms discovery). Claude selects among potentially 100+ skills on description alone.
- **Progressive disclosure (3 tiers)**: (1) name+description preloaded at startup (~100 tokens/skill); (2) SKILL.md body loaded on activation; (3) reference files/scripts loaded or executed only as needed — no context cost until read.
- **Line-count recommendation**: keep **SKILL.md body under 500 lines**; split into reference files when approaching that. Keep references **one level deep** from SKILL.md (deeply nested reference chains cause partial `head -100`-style reads). Reference files >100 lines should start with a table of contents.
- **Degrees of freedom**: match specificity to task fragility — high freedom (heuristics) for open tasks, exact scripts ("run exactly this, don't modify") for fragile sequences.
- **Workflows & feedback loops**: checklists Claude copies and ticks off; validator→fix→repeat loops; plan-validate-execute with machine-verifiable intermediate files for batch/destructive operations.
- **Scripts over generated code** for deterministic operations: bundled utility scripts are more reliable, cost no context tokens until executed; make execution vs read-as-reference intent explicit; handle errors inside scripts ("solve, don't defer"); no magic constants.
- **Evaluation-driven development**: build ≥3 evaluations BEFORE writing extensive docs; baseline without the skill; iterate with a "Claude A authors / Claude B tests" loop; test across Haiku/Sonnet/Opus. (No built-in eval runner exists — roll your own.)
- **Naming**: lowercase-hyphenated; gerund form suggested (`processing-pdfs`); avoid `helper`/`utils`; reserved words "anthropic"/"claude" banned in names.
- **Content hygiene**: no time-sensitive info (use an "old patterns" collapsed section instead); consistent terminology; forward-slash paths only (Windows-style backslashes are an explicit anti-pattern); don't offer many alternative approaches — give one default plus an escape hatch; fully-qualify MCP tool names (`Server:tool`).

### What changed / is notable for 2025→2026

- **Oct 2025**: Skills launched; engineering post (2025-10-16) established progressive disclosure, name/description criticality, and "split when SKILL.md becomes unwieldy". Original spec: only `name` + `description` frontmatter.
- **Since then** the platform best-practices doc formalized hard numbers and structure rules: 64/1,024-char field limits, reserved-word ban, 500-line body ceiling, one-level-deep references, TOC-for->100-line files, third-person description rule, eval-first workflow, model-matrix testing.
- **Frontmatter field set expanded and forked by surface**: portable/spec fields = `name`, `description`, `license`, `compatibility`, `metadata`, `allowed-tools` (accepted by claude.ai uploads, Skills API, package_skill.py). Claude Code adds non-portable fields (`context: fork`, `disable-model-invocation`, `arguments`/`argument-hint`, `model`, dynamic `!` context injection, `${CLAUDE_SKILL_DIR}` substitution). A publicly distributed skill targeting claude.ai must use ONLY the portable set or uploads fail validation. (Source: code.claude.com/docs/en/skills compatibility table.)
- **Commands merged into skills** in Claude Code (`.claude/commands/` files still work but skills are the recommended form).
- **claude.ai ↔ Claude Code sync** of enabled skills now exists (`~/.claude/skills/synced/`), with restrictions: `!` dynamic-context commands and `@` file references in synced skills are neutered outside their home surface.
- `compatibility` field semantics (declaring which surfaces/environments a skill supports) appear in the allowlist but detailed official documentation of its value format was **UNCONFIRMED** in this pass.

---

## Flags / UNCONFIRMED summary

1. Whether the claude.ai upload dialog accepts a `.skill` extension directly, or requires renaming to `.zip` — official docs only say ZIP; third-party guides report renaming works. Ship `.zip`.
2. Exact claude.ai skill-ZIP size limit — "limits exist" per Help Center, number unpublished. API doc says <30 MB uncompressed; claude.ai per-file transfer cap is 30 MB. Assume ≤30 MB.
3. Description length enforced by the claude.ai uploader: 200 (Help Center) vs 1,024 (platform docs). Target ≤200 to be safe.
4. `compatibility` frontmatter value format — field exists in allowlist; detailed spec not verified.
5. Whether the support article's downloadable-file-type list (xlsx/pptx/docx/pdf/png) is a hard allowlist — contradicted by Anthropic's own skill-creator claiming downloadable `.skill` output on Claude.ai; treated as non-exhaustive.
