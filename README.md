# AI Agent Knowledge Bank

A personal collection of agentic skills, workflows, and resources I use — a mix of custom ones I've built and links to other resources.

The skills and scaffolding here are primarily designed around [Claude Code](https://claude.ai/code), though the patterns and ideas are transferable to other agentic setups — I've conducted some tests in [Gemini CLI](https://cloud.google.com/gemini-cli) and [OpenCode](https://opencode.ai).

## Install

A subset of the skills and workflows I currently use as outlined in the `CLAUDE.md` file are available to install into any project. Clone this repo, then from your project root run:

```bash
mkdir -p .claude && cp -r ~/ai-agent-knowledge-bank/skills .claude/ && cp ~/ai-agent-knowledge-bank/CLAUDE.md .
```

> Adjust the path if you cloned the repo somewhere other than `~`.

This copies all skills into your project's `.claude/skills/` directory (so they're available in cloud/autonomous sessions) and drops `CLAUDE.md` at your project root.

> I'm still exploring the best way to package and share this as a proper Claude Code plugin — will update once the workflow has stabilised.

For guidance on how to instruct agents to use these skills together, see the [`CLAUDE.md`](CLAUDE.md) file in this repo. It's intentionally generic — adapt it for bespoke project setups by pointing to project-specific architecture docs, conventions, or workflows as needed.

---

## Install contents

| Skill | Trigger | Description | Source |
|-------|---------|-------------|--------|
| [plan-recap](skills/plan-recap/SKILL.md) | `/plan-recap` | Reads planning docs and produces a concise session briefing | Custom |
| [summarise-session](skills/summarise-session/SKILL.md) | `/summarise-session` | Writes a structured `WORK_SUMMARY_DDMMYY.md` from the current session | Custom |
| [create-prd](skills/create-prd/SKILL.md) | `/create-prd` | Creates a Product Requirements Document covering problem, personas, user stories, and technical slices | Custom, influenced by [cexll/myclaude](https://www.skills.sh/cexll/myclaude/product-requirements) |
| [create-technical-spec](skills/create-technical-spec/SKILL.md) | `/create-technical-spec` | Creates a detailed, implementable technical spec for a single slice of work | Custom, influenced by [github/awesome-copilot](https://www.skills.sh/github/awesome-copilot/create-specification) |
| [daily-summary](skills/daily-summary/SKILL.md) | `/daily-summary` | Pulls Granola meeting notes and writes a dated daily summary with action items | Custom |
| [daily-plan](skills/daily-plan/SKILL.md) | `/daily-plan` | Builds the next day's `to_do_DDMMYYYY.md` from unfinished tasks and meeting actions | Custom |
| [grill-me](skills/grill-me/SKILL.md) | `/grill-me` | Stress-tests a plan by interviewing you until reaching shared understanding | [Matt Pocock](https://github.com/mattpocock/skills) |
| [prototype](skills/prototype/SKILL.md) | `/prototype` | Builds throwaway code to sanity-check a state model or UI direction before real implementation | [Matt Pocock](https://github.com/mattpocock/skills), with some of my minor customisations added |
| [tdd](skills/tdd/SKILL.md) | `/tdd` | Red-green-refactor TDD workflow with vertical slices | [Matt Pocock](https://github.com/mattpocock/skills) |
| [testing](skills/testing/SKILL.md) | `/testing` | Test patterns, factories, and anti-patterns for behaviour-driven tests | [Gidi Morris](https://github.com/gmmorris/xp-and-lean-agent-specification) |
| [agent-browser](skills/agent-browser/SKILL.md) | `/agent-browser` | Browser automation CLI for AI agents via CDP | [Vercel Labs](https://www.skills.sh/vercel-labs/agent-browser/agent-browser) |
| [documentation-and-adrs](skills/documentation-and-adrs/SKILL.md) | `/documentation-and-adrs` | Records architectural decisions and ensures README exists | [Gidi Morris](https://github.com/gmmorris/xp-and-lean-agent-specification) |
| [generating-novel-ideas](skills/generating-novel-ideas/SKILL.md) | `/generating-novel-ideas` | Structured ideation workflow — search process across independent idea pools, analogy transfer, contradiction resolution, and portfolio of finalists with tests | [Tristan Manchester](https://github.com/tristanmanchester/agent-skills/tree/main/generating-novel-ideas) |
| [simplify](skills/simplify/SKILL.md) | `/simplify` | Reviews recently modified code for clarity and consistency, then applies refinements without changing functionality | [brianlovin/agent-config](https://github.com/brianlovin/agent-config/tree/main), with some of my customisations added |
| [security-reviewer](skills/security-reviewer/SKILL.md) | `/security-reviewer` | Reviews code for injection vulnerabilities, auth flaws, secrets, and data-handling issues | [brianlovin/agent-config](https://github.com/brianlovin/agent-config/tree/main), with some of my customisations added |

---

## Custom skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| [plan-recap](skills/plan-recap/SKILL.md) | `/plan-recap` | Reads planning and work summary docs from the current project and produces a concise session briefing |
| [summarise-session](skills/summarise-session/SKILL.md) | `/summarise-session` | Reviews the current conversation and writes a structured summary to a `WORK_SUMMARY_DDMMYY.md` file |
| [daily-summary](skills/daily-summary/SKILL.md) | `/daily-summary` | Pulls today's Granola meeting notes and writes a dated daily summary with consolidated action items |
| [daily-plan](skills/daily-plan/SKILL.md) | `/daily-plan` | Builds the next working day's `to_do_DDMMYYYY.md` by carrying over unfinished tasks and pulling in meeting actions |
| [create-prd](skills/create-prd/SKILL.md) | `/create-prd` | Produces a PRD (problem, personas, user stories, technical slices) ahead of technical spec work — an interview scoped only to filling template gaps, not a plan stress-test (that's `/grill-me`) |
| [create-technical-spec](skills/create-technical-spec/SKILL.md) | `/create-technical-spec` | Produces one implementable technical spec for a single slice of work, precise enough for an agent with no other context to build from |

## Skills from others

| Skill | Source | Description |
|-------|--------|-------------|
| agent-browser | [skills.sh](https://www.skills.sh/vercel-labs/agent-browser/agent-browser) | Browser automation CLI for AI agents — fast, persistent browser control with accessibility-tree snapshots and session management |
| xp-and-lean-agent-specification | [GitHub](https://github.com/gmmorris/xp-and-lean-agent-specification) | Gidi Morris's XP and lean principles applied to agentic workflows — skills and specifications for structured, disciplined AI-assisted development |
| skills | [GitHub](https://github.com/mattpocock/skills) | Matt Pocock's collection of Claude Code skills |
| agent-skills | [GitHub](https://github.com/tristanmanchester/agent-skills) | Tristan Manchester's agent skills — includes `generating-novel-ideas`, a structured ideation workflow that treats idea generation as a search process rather than list-making |
| gstack | [GitHub](https://github.com/garrytan/gstack) | Garry Tan's personal AI development stack and tooling setup |
| gbrain | [GitHub](https://github.com/garrytan/gbrain) | Garry Tan's gbrain — an architectural pattern for agentic memory and reasoning worth exploring |
| agent-config | [GitHub](https://github.com/brianlovin/agent-config/tree/main) | Brian Lovin's agent/skill configs — source for `simplify` and `security-reviewer`, both lightly customised (see their SKILL.md files for what changed) |

## Plugins from others

[Claude Code plugins](https://claude.com/plugins) are a way to package multiple skills, agents, and workflows together into a single installable unit — useful for shipping opinionated setups for a given domain or workflow.

| Plugin | Description |
|--------|-------------|
| [frontend-design](https://claude.com/plugins/frontend-design) | Create distinctive, production-grade frontend interfaces with high design quality |
| [feature-dev](https://claude.com/plugins/feature-dev) | End-to-end feature development workflow — from spec to working, tested code |

I'm exploring how to package opinionated plugin packs for specific workflows (e.g. data analysis) — bringing together the right skills, agents, and scaffolding in one place.

## Learnings & gotchas

Practical tips and gotchas collected as I go. See [LEARNINGS_AND_GOTCHAS.md](LEARNINGS_AND_GOTCHAS.md) for the full list with detail.

## OpenCode

I've started exploring [OpenCode](https://opencode.ai) as an alternative during recent Claude outages.

Not a like-for-like migration from Claude Code, but close out of the box with minimal setup.

| Observation | Detail |
|-------------|--------|
| Free models work for basic tasks | Out-of-the-box free models (like Big Pickle) handle simple tasks fine |
| Permission model defaults to "allow" | You must explicitly set `permission: { "*": "ask" }` in `opencode.json`, otherwise it won't prompt |
| CLAUDE.md loaded automatically | The project's `CLAUDE.md` is read on every session start — no migration needed |
| Skills load across projects | Skills in `.claude/skills/` are available via the `skill` tool, not just `/command` triggers |

## Other helpful resources

| Resource | Description |
|----------|-------------|
| [Claude Code Mastery](https://arps18.github.io/posts/claude-code-mastery/) | Practical guide to getting the most out of Claude Code, including a curated list of popular CLAUDE.md files worth studying |
| [Working with AI](https://eugeneyan.com//writing/working-with-ai/) | Eugene Yan's reflections on day-to-day AI-assisted work — patterns, pitfalls, and how to stay in the driver's seat |
| [autoresearch](https://github.com/karpathy/autoresearch) | Andrej Karpathy's autonomous research agent — a reference implementation for multi-step agentic research workflows |
| [Levels of Agentic Engineering](https://www.bassimeledath.com/blog/levels-of-agentic-engineering) | A framework for thinking about how much autonomy to hand to AI agents at different stages of a project |
| [Agentic Engineering Patterns](https://simonwillison.net/guides/agentic-engineering-patterns/) | Simon Willison's guide to recurring patterns in agentic systems — practical and grounded in real-world experience |
