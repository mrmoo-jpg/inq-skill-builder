# Competitive Landscape Scan — INQ Skill Builder
**Date:** 2026-08-13 | **Method:** Web survey of public skill-creation tools for Claude (official, open-source, commercial/content)

INQ Skill Builder's planned differentiators, used as the scoring rubric:
1. **D1 — Research-style elicitation** (artifact scanning + knowledge-extraction interview, not a form)
2. **D2 — Teach-while-building** explanations
3. **D3 — Enduring-principles vs dated-tactics file architecture with freshness stamps**
4. **D4 — Build manifests enabling later skill health-checks**
5. **D5 — Zero-setup operation on Claude.ai**

---

## 1. Anthropic skill-creator (official skill/plugin)
- **Sources:** https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md · https://claude.com/plugins/skill-creator · https://medium.com/all-about-claude/i-tested-anthropics-skill-creator-plugin-on-my-own-skills-here-s-what-i-found-23ad406b0825
- **Who it's for:** Explicitly broad — its own text names non-technical users ("plumbers, parents, grandparents") through experienced developers. In practice, most usage is by Claude Code users; it also ships built into Claude Desktop/Cowork.
- **Approach:** Structured iterative loop with four modes (Create / Eval / Improve / Benchmark). Starts with an *interview* to capture intent, triggers, output format; then writes SKILL.md with progressive disclosure; then eval loop.
- **Tests skills?** Yes — the strongest in the field. Parallel baseline vs with-skill subagent runs, LLM grading against assertions, benchmark.json with mean/stddev on pass rate, tokens, timing, plus an interactive HTML review viewer, and a description-triggering optimization loop.
- **Vs. differentiators:**
  - D1: *Partial.* Interviews the user about intent/edge cases, but it is a requirements interview for the skill spec, not a knowledge-extraction interview mining the user's expertise, and no artifact scanning.
  - D2: *Partial.* Has explicit pedagogy rules — explains jargon contextually, favors "why" over MUST/NEVER — but this is aimed at making the *skill text* teachable to the model, not systematically teaching the *user* how skills work as they build.
  - D3: No. No freshness stamps or principles/tactics file split.
  - D4: *Partial-adjacent.* Generates eval/benchmark manifests (evals.json, grading.json, benchmark.json) that could support re-testing, but no build manifest designed for later health-checks of the skill's content.
  - D5: Yes on Claude Desktop/Cowork (pre-installed); the full eval loop assumes Claude Code-style subagents and scripts — not the plain Claude.ai chat experience.
- **Verdict:** The reference competitor. Beats INQ on evals; weaker on true knowledge extraction, user education, and longevity architecture.

## 2. obra/superpowers — writing-skills (Jesse Vincent)
- **Sources:** https://github.com/obra/superpowers/blob/main/skills/writing-skills/SKILL.md · https://blog.fsck.com/2025/10/09/superpowers/ · https://deepwiki.com/obra/superpowers/8.1-test-driven-development-for-skills
- **Who it's for:** Serious Claude Code power users / developers inside the Superpowers methodology framework.
- **Approach:** Freeform but doctrinally strict: TDD applied to documentation (RED-GREEN-REFACTOR). You run "pressure scenarios" with subagents *without* the skill first, write minimal documentation targeting observed failures, then iterate to close loopholes. The companion brainstorming skill uses Socratic dialogue to force users to articulate what they actually want.
- **Tests skills?** Yes — empirical baseline testing is the core principle: "if you didn't watch an agent fail without the skill, you don't know if the skill teaches the right thing."
- **Vs. differentiators:** D1 partial (Socratic brainstorming is genuine elicitation, but of goals, not domain knowledge; no artifact scanning). D2 no (teaches the methodology, assumes a motivated engineer). D3 no. D4 no. D5 no (Claude Code plugin, setup required).
- **Verdict:** Best-in-class rigor, deliberately hostile to hand-holding. Zero overlap with the non-engineer audience.

## 3. alirezarezvani/claude-code-skill-factory
- **Source:** https://github.com/alirezarezvani/claude-code-skill-factory (companion library: https://github.com/alirezarezvani/claude-skills)
- **Who it's for:** Developers/teams generating skills, agents, prompts, and hooks at scale; multi-agent-CLI interoperability (Codex, Cursor, Gemini CLI).
- **Approach:** Wizard + templates. Five guide agents each ask 4-7 structured questions, plus factory prompt templates and generator skills. Closest thing in the field to a "form" — exactly the interaction model INQ wants to avoid.
- **Tests skills?** Light — a `/validate-output` slash command validates structure of generated content; no behavioral eval loop.
- **Vs. differentiators:** D1 no (fixed-question wizard, the anti-pattern). D2 no. D3 weak (repo keeps a CHANGELOG, but generated artifacts get no freshness stamps). D4 no. D5 no (Claude Code repo setup).
- **Verdict:** Volume/template play for developers. Validates that "wizard" is the default competitor interaction model.

## 4. somasays/skill-creator (docs-to-skill)
- **Source:** https://github.com/somasays/skill-creator
- **Who it's for:** Developers who want drop-in skills teaching Claude framework patterns from existing documentation.
- **Approach:** Multi-step: intent capture → research → interview about relevant patterns → package. Notable because it *does* start from existing artifacts (documentation) rather than a blank form.
- **Tests skills?** Mentions "testing and packaging" as stages but documents no mechanism. Minimal activity (2 commits) — more prototype than product.
- **Vs. differentiators:** D1 *partial* — docs-scanning is the closest public analog to INQ's artifact scanning, but aimed at framework docs for coders, not a person's own work artifacts, and no knowledge-extraction interview of the human. D2 no. D3 no. D4 no. D5 no.
- **Verdict:** Proof that artifact-derived skills resonate; execution is thin and developer-targeted.

## 5. metaskills/skill-builder
- **Source:** https://github.com/metaskills/skill-builder
- **Who it's for:** Claude Code users; also covers converting subagents into skills. ~111 stars.
- **Approach:** Template + reference driven (SKILL.md guidance, `templates/`, `reference/` dirs). Freeform authoring aid rather than a guided builder.
- **Tests skills?** Not evident.
- **Vs. differentiators:** None of the five. D5 no.

## 6. Skill audit/maintenance tools (Skill Stocktake, skill-doctor, Skill Reviewer, et al.)
- **Sources:** https://mcpmarket.com/tools/skills/skill-stocktake · https://lobehub.com/skills/amaljithkuttamath-skill-doctor-skill-doctor · https://mcpmarket.com/tools/skills/skill-reviewer-auditor
- **Who it's for:** Claude Code users with accumulating skill libraries.
- **Approach:** Post-hoc auditing, not building. Skill Stocktake audits local/global skills against a quality checklist and issues Keep/Improve/Update/Retire verdicts. skill-doctor runs Intake/Checkup/Consult modes to discover, diagnose, and recommend fixes for skills and agents.
- **Tests skills?** Static/heuristic inspection, not behavioral evals.
- **Vs. differentiators:** D4 *partial* — these prove demand for skill health-checks, but they reconstruct state by inspection because no builder left a manifest behind. None do freshness stamping at build time (D3). D5 no.
- **Verdict:** The existence of a whole aftermarket repair category is the strongest validation of INQ's build-manifest thesis: everyone else treats health as an afterthought.

## 7. Anthropic "Complete Guide to Building Skills for Claude" (content, free PDF)
- **Sources:** https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf · https://gist.github.com/joyrexus/ff71917b4fc0a2cbc84974212da34a4a · https://claude.com/blog/how-to-create-skills-key-steps-limitations-and-examples
- **What:** Official educational content covering fundamentals, planning, testing, distribution, patterns, YAML frontmatter. For anyone; passive reading, not a tool. Covers D2 territory as static content only; no elicitation, no stamps, no manifests.

## 8. Commercial skill packs (Gumroad): "Claude Skills Pack" (Prompt Guy), "300+ Claude Skills" (Usama Akram)
- **Sources:** https://thinkaiprompt.gumroad.com/l/claude-skills · https://usamaakrm.gumroad.com/l/claude-skills
- **Who it's for:** Non-technical business users (founders, marketers, operators) — INQ's audience, notably.
- **Approach:** Pre-built static packs (30 and 300+ skills by business function: writing, sales, legal, HR, SEO...). "Install once, use forever" pitch. They sell *finished skills*, not the ability to build your own; untested, undated, no maintenance story.
- **Vs. differentiators:** None. But they demonstrate a paying non-technical market and, implicitly, its ceiling: generic skills that can't encode the buyer's own expertise — the exact gap INQ's knowledge-extraction interview fills.

## 9. Courses & creator content: "Claude Code for Designers" (aidesignlab), "Complete Claude Marketing Course for Non-Techies", MindStudio's SOP-to-skill workflow posts
- **Sources:** https://aidesignlab.gumroad.com/l/claude-code-for-designers · https://brockster6202.gumroad.com/l/xbcfrf · https://www.mindstudio.ai/blog/skill-creator-workflow-claude-code-sop-to-skill · https://claudereadiness.com/blog/claude-sop-creation/
- **Who it's for:** Designers and non-technical marketers — INQ's audience again.
- **Approach:** Video/text courses plus bundled skill files (Claude Code for Designers ships 300+ skill files and 9 projects). MindStudio and ClaudeReadiness publish the closest conceptual neighbor to D1: interview a subject-matter expert (scope → triggers → steps → exceptions → quality measures), feed transcripts to Claude, generate an SOP, then convert the SOP to a skill — but as a manual blog-post recipe, not a product.
- **Vs. differentiators:** D1 exists here only as content/recipe; D2 is what a course *is* (but decoupled from the building moment); D3/D4/D5 absent.

## Also noted (not full entries)
- **Marketplaces/registries** — skills.sh, SkillsMP, Tessl registry, mcpmarket.com, claudemarketplaces.com, plus curated lists (https://github.com/travisvn/awesome-claude-skills, https://github.com/karanb192/awesome-claude-skills, https://github.com/ComposioHQ/awesome-claude-skills): distribution, not creation. None surface freshness/staleness metadata — a listing-side echo of the D3 gap.
- **anthropics/claude-code plugin-dev skill-development skill** (https://github.com/anthropics/claude-code/blob/main/plugins/plugin-dev/skills/skill-development/references/skill-creator-original.md) — developer-facing sibling of the official skill-creator.

---

## Conclusion

### Genuinely unoccupied territory
- **D3 — Principles-vs-tactics architecture with freshness stamps.** No tool, list, or marketplace dates skill content or separates enduring guidance from perishable tactics. The entire aftermarket audit category (Skill Stocktake, skill-doctor) exists because nothing is stamped at build time. Clearest open ground.
- **D4 — Build manifests for later health-checks.** Audit tools reconstruct skill state by inspection; Anthropic's eval manifests serve benchmarking, not longevity. Nobody writes a build-time record designed for future re-verification.
- **D1's specific form — knowledge-extraction interview + scanning the user's own artifacts.** Interviews exist everywhere, but always as *requirements* interviews about the desired skill. Extracting the user's tacit expertise (research-elicitation style) and mining their existing documents exists only as blog recipes (MindStudio, ClaudeReadiness) and a docs-for-frameworks prototype (somasays). No product does it, and none for non-engineers.

### Partial competition
- **D2 — Teach-while-building:** Anthropic's skill-creator has real pedagogical rules (jargon gating, explain-the-why), and its free Complete Guide plus paid courses cover the education separately. INQ's edge is fusing teaching into the build moment, not owning teaching outright.
- **D5 — Zero-setup:** Anthropic's skill-creator ships pre-installed in Claude Desktop/Cowork, which is close to zero-setup — though its eval loop leans on Claude Code machinery, and plain Claude.ai chat remains underserved. Treat D5 as a shrinking moat, not a durable one.
- **D1's interview surface:** skill-creator's intent interview, Superpowers' Socratic brainstorming, and skill-factory's wizards all occupy adjacent space; differentiation must come from *what* is elicited (domain knowledge), not *that* there is dialogue.

### Ideas worth stealing
1. **Baseline-first pressure testing (obra/superpowers, writing-skills):** run a scenario *without* the skill first so the user sees the failure the skill fixes — "if you didn't watch an agent fail without the skill, you don't know if it teaches the right thing." Cheap to adapt as a lightweight before/after demo inside Claude.ai, and it doubles as a teach-while-building moment.
2. **Description-triggering optimization (Anthropic skill-creator):** a dedicated loop to fight undertriggering, since "descriptions are everything" for activation (echoed independently by somasays). Non-engineers will write timid descriptions; an automated trigger-check is high-value and easy to explain.
3. **Verdict-style health output (Skill Stocktake):** Keep / Improve / Update / Retire is a perfect non-engineer-friendly vocabulary for INQ's manifest-driven health-checks — plain-language verdicts rather than diff reports, powered by the build manifest competitors don't have.
