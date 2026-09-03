# Learnings & Gotchas

A collection of practical tips I've picked up along the way and gotchas to watch out for.

| Topic | Summary |
|-------|---------|
| [Skills in cloud sessions](#skills-are-not-available-in-cloudautonomous-sessions-unless-committed-to-the-repo) | Project-scope your skills if you want them available in cloud-based autonomous sessions — user-level skills live on your machine and won't be cloned |
| [Private repo access for Claude Code web](#granting-claude-code-web-access-to-a-private-github-repo) | OAuth connection alone isn't enough — you must also grant access via https://github.com/apps/claude/installations/select_target |
| [Agent SDK billing moves to plan-based (June 2025)](#agent-sdk-billing-is-moving-to-plan-based-from-june-15-2025) | From 15 Jun 2025 you can use your Claude plan instead of a separate API key for cloud agents — migrate to get a consistent local/cloud auth pattern |
| [No visual browser review in Claude Code web](#visual-browser-review-is-not-available-in-claude-code-web) | Chrome/agent-browser is blocked in the web environment — do UI validation locally in the CLI or desktop app instead |
| [Fable 5 can build non-trivial apps end-to-end in one session](#fable-5-is-capable-enough-to-build-non-trivial-apps-end-to-end-in-a-single-session) | Built a fully functional personal wiki from ~500 articles in ~10 mins |
| [Autonomous sessions are structurally more token-hungry](#autonomous-sessions-are-structurally-more-token-hungry-than-interactive-ones) | No human checkpoints + greater parallel sub-agent spawning means consumption compounds fast — set conservative scope and be even more explicit about what not to do |
| [Model and effort level compound on longer tasks](#model-and-effort-level-compound-significantly-on-longer-tasks) | Opus/xhigh vs Sonnet/low creates a very wide cost range with no pre-task tooling to guide the choice — default conservative and escalate only if quality is insufficient |
| [Significant gap in pre-task cost visibility](#significant-gap-in-pre-task-cost-visibility) | No way to estimate cost or whether a task fits within plan limits before running — actively investigating; early approach using post-session data to build predictions: github.com/CodeSarthak/tarmac |
| [Write incrementally on longer tasks](#write-incrementally-on-longer-tasks-to-survive-budget-limits) | Commit work frequently so budget limits leave you with recoverable checkpoints — in Claude Code web, instruct the agent to raise a PR before it expects to hit limits |
| [TDD and testing patterns not yet reliable for autonomous sessions](#tdd-and-testing-patterns-are-not-yet-reliable-for-autonomous-sessions) | TDD adherence is patchy without explicit prompting — especially for front-end changes. agent-browser post-build visual validation is the reliable fallback |
| [Architecture and docs files often skipped without explicit instruction](#architecture-and-documentation-files-are-often-skipped-without-explicit-instruction) | Agents skip ARCHITECTURE*.md and README files unless explicitly told to read them — adding a dedicated CLAUDE.md instruction appears to fix this |
| [Claude defaults to its own memory folders over project .md files](#claude-defaults-to-writing-memory-into-its-own-hidden-folders-rather-than-project-md-files) | Claude consistently writes session context to `~/.claude/projects/.../memory/` rather than visible project docs — less transparent, not version-controlled |
| [Refactoring and simplification require intentional triggering](#refactoring-and-simplification-require-intentional-triggering) | Agents quietly accumulate debt across sessions — global variables, duplicated logic, design drift. Needs explicit CLAUDE.md patterns and a session-close refactor checkpoint |
| [Skills as orchestration layer for personal productivity apps](#skills-as-orchestration-layer-for-personal-productivity-apps) | Claude Code skills work well as orchestration for multi-step personal workflows — markdown outputs, schedulable, low overhead to extend. Main fragility: no structured failure handling |
| [Artifact-design skill for internal technical communication](#artifact-design-skill-for-internal-technical-communication) | Promising early results using /artifact-design to produce polished, self-contained HTML artifacts for communicating technical concepts and designs internally |
| [claude-api skill dumps all docs when language can't be inferred](#claude-api-skill-dumps-all-language-docs-when-it-cant-infer-the-project-language) | With no .py/.ts/etc. source files yet in the repo, the skill's language-detection fallback silently injected every language's reference docs (~309k tokens) instead of asking which language to show |
| [Custom subagents + skill orchestration for parallel research](#custom-subagents--skill-orchestration-for-parallel-research) | Early encouraging result: a project `.claude/agents/` subagent doing read-only research, fanned out in parallel and orchestrated by a skill that serializes writes, cut wall-clock time with no correctness issues |
| [Spec-driven development pays off mainly in fully autonomous mode](#spec-driven-development-pays-off-mainly-in-fully-autonomous-mode) | In a co-pilot/steward pattern, a highly detailed spec (e.g. from `/grill-me`) doesn't drive the expected efficiency gains — ends up debated and expanded live anyway, especially when design thinking is meant to evolve during the build |
| [OpenRouter routing doesn't guarantee lowest price unless you set a strategy](#openrouter-routing-doesnt-guarantee-lowest-price-unless-you-set-a-strategy) | Default routing balances low price against high uptime — it will not always pick the cheapest provider/quote for a model. Set the routing strategy explicitly if you want bounded/predictable cost |
| [Claude Code web AI workflows need extra infra setup](#claude-code-web-ai-workflows-need-extra-infra-setup) | Beyond the repo: a separate API key per external service (e.g. OpenRouter), an allowed-domains list (e.g. openrouter.ai), and awareness that the local CLI can now push workflows back to the cloud once started — be intentional about whether you want that |

---

## Skills are not available in cloud/autonomous sessions unless committed to the repo

When you run a cloud-based autonomous session (e.g. via claude.ai tasks or remote agents), Claude starts from a fresh clone of whatever repo you point it at. Only what's committed to that repo is available.

**What IS available in cloud sessions:**

Anything in the cloned repo is available, including:
- `CLAUDE.md`
- `.claude/settings.json` hooks
- `.mcp.json` MCP servers
- `.claude/rules/`, `.claude/skills/`, `.claude/agents/`, `.claude/commands/`

**What is NOT available:**

- Your user-level `~/.claude/CLAUDE.md` — lives on your machine, not in the repo
- Your user-level `~/.claude/skills/`, `~/.claude/agents/`, `~/.claude/commands/`

**Practical implications:**

- Skills stored locally or referenced externally (like this bank) won't be available to cloud sessions unless you commit them into the target repo under `.claude/skills/`
- If you want skills available across cloud tasks, commit them into each repo's `.claude/` directory, or enable them on claude.ai itself (skills enabled on claude.ai are loaded into cloud sessions automatically)

The "skills you enable on claude.ai are loaded automatically" note is worth exploring as a way to make your skills available without committing them to every repo.

---

## Agent SDK billing is moving to plan-based (from June 15 2025)

Until mid-June 2025 the recommended pattern for using Claude agents in cloud/remote setups was to authenticate with a separate, directly-billed API key — which meant a mixed pattern: API key in cloud, Claude plan locally.

From 15 June 2025 onwards you can use your existing Claude plan to authenticate the Agent SDK, removing the need for a separate API key. This applies to both Claude Code agents and Anthropic SDK-based agents.

**Reference:** [Use the Claude Agent SDK with your Claude plan](https://support.claude.com/en/articles/15036540-use-the-claude-agent-sdk-with-your-claude-plan)

**Practical implication:** If you currently use a mixed authentication pattern, migrate to plan-based auth once the rollout lands so local and cloud sessions behave consistently.

---

## Visual browser review is not available in Claude Code web

The `/agent-browser` skill (and any Chrome-based validation) does not work in Claude Code web sessions. Chrome downloads are blocked by the environment's network policy, so UI checks must be done via other means (e.g. `TestClient`, `curl`, or manual inspection).

**Practical implication:** If your workflow relies on `agent-browser` for automated UI validation, run those sessions locally in the desktop/CLI app rather than through claude.ai/code.

---

## Fable 5 is capable enough to build non-trivial apps end-to-end in a single session

First experiment with Fable 5: built a personal wiki (inspired by [karpathy's reading-list gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) and [rohitg00's wiki gist](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2)) from ~500 historic articles in ~10 minutes. The output included a fully functional update system and a browsable front-end — no hand-holding required.

**Practical implication:** Fable 5 is worth reaching for on ambitious single-session builds.

---

## Granting Claude Code web access to a private GitHub repo

When connecting a private repo to Claude Code web, the GitHub OAuth connection alone is not enough. You also need to explicitly grant the Claude GitHub app access to the specific repo.

Go to: **https://github.com/apps/claude/installations/select_target**

After 2FA, you can select which repositories the Claude app can access. Add the private repo there and it will become available to Claude Code web sessions.

---

## Autonomous sessions are structurally more token-hungry than interactive ones

Autonomous sessions consume tokens faster than interactive ones for two reasons: the model has no human checkpoints to catch and halt wasteful trajectories, and it appears to more readily spawn greater multiples of parallel sub-agent paths, so consumption compounds quickly.

Task budgets exist as a partial mitigation — they allow setting token ceilings — but are scoped to the API and not available on the Claude plan: https://platform.claude.com/docs/en/build-with-claude/task-budgets

**Practical implication:** Treat autonomous runs as a higher-cost mode by default. Set conservative scope, be explicit about what the agent should *not* do, and consider defining a stopping condition for more open-ended tasks. In-process budget awareness is an open area: injecting a token/time budget into the system prompt and asking the agent to self-report at a threshold is one low-tech approach worth exploring.

---

## Model and effort level compound significantly on longer tasks

The combination of model tier and effort level creates a very wide cost range. Choosing Opus at `/xhigh` vs Sonnet at `/low` for the same task can produce radically different token consumption. This is particularly significant on long-running tasks where that multiplier applies across many calls.

There is currently no tooling to tell you which combination a task warrants before you run it, and no transparency into how different combinations map to tiered plan limits. Empirical experiments are needed to build intuition.

**Practical implication:** Default to a conservative model/effort combination and escalate only if quality is demonstrably insufficient.

---

## Significant gap in pre-task cost visibility

Two related gaps: (1) there is no way to estimate what a task will cost before running it; (2) there is no mapping from estimated cost to plan tier limits, so you cannot answer "will this task be possible within my limits?" without just trying.

This is an area actively under investigation. One interesting early approach is collecting usage and cost data post-session to build up predictions over time: https://github.com/CodeSarthak/tarmac

---

## TDD and testing patterns are not yet reliable for autonomous sessions

TDD and testing pattern adherence is patchy in practice. Common gaps observed:

- Rarely runs one test at a time in a true red-green cycle — tends to write several tests then implement, or implement first and backfill tests
- Will not independently design repeatable integration or smoke tests without being asked
- For front-end changes especially, test-first discipline is unlikely to emerge without explicit prompting

**What does work well:** When the `/agent-browser` skill is loaded with guidance to validate post-build, it reliably runs through visual validation autonomously — browsing, navigating, screenshotting, and reading the result. This closes the development loop effectively even without formal tests.

**Practical implication:** Do not rely on autonomous sessions to self-enforce TDD rigour. Continue to investigate what guardrails and harness adjustments (CLAUDE.md instructions, hooks, skill updates) are needed to make this work more consistently before using in higher-stakes autonomous workflows.

---

## Write incrementally on longer tasks to survive budget limits

Until better cost/limit controls exist, structure longer autonomous tasks to commit or checkpoint work frequently rather than accumulating everything in memory until the end. If the session hits a budget ceiling mid-task, incremental commits mean you have recoverable progress to build from rather than starting over.

A related pattern in Claude Code web: instruct the agent to pause before it expects to hit limits and raise a PR with whatever is complete. This gets work out of the session and into a reviewable state before the session is blocked, and gives a clean handoff point for continuing in a new session.

---

## Architecture and documentation files are often skipped without explicit instruction

Observed that agents will frequently skip reading `ARCHITECTURE*.md` and `README` files even when they are present and clearly relevant — they proceed straight to coding without building an understanding of the system first. This leads to changes that miss important context or contradict documented decisions.

**Practical fix:** Be maximally explicit in `CLAUDE.md`. Adding a dedicated instruction — "Always look for and read any ARCHITECTURE*.md files or README files across the project (including in subdirectories and modules) — make sure you review documentation and understand the codebase before you proceed" — appears to address this in initial testing.

**Status:** Anecdotal testing supports the fix working, but this warrants more systematic experimentation to confirm reliability across different task types and session modes.

**Related:** [[CLAUDE.md architecture reading instruction]]

---

## Claude defaults to writing memory into its own hidden folders rather than project .md files

Observed consistent pattern: when asked to "remember" something or when saving session context, Claude will write to its own internal memory directories (e.g. `~/.claude/projects/.../memory/`) rather than updating visible project documentation like `WORK_SUMMARY*.md` or `LEARNINGS_AND_GOTCHAS.md`.

This is less transparent (files are hidden from normal project navigation), less portable (memory is machine/session scoped), and harder to review or correct. The project `.md` file pattern is preferable: it lives in the repo, is readable by any tool, and is version-controlled.

**Fix applied:** Adding explicit instruction to `CLAUDE.md` — "Do not write to Claude's internal memory folders. Persist session context and learnings by updating project `.md` files directly." — steers toward the more transparent pattern.

**Status:** Partial improvement. The `CLAUDE.md` instruction reduces the behaviour but does not eliminate it — occasional edge cases remain where Claude still attempts to write to `memory/` files, particularly when the auto-memory system prompt instructions override project-level guidance. Requires active vigilance and correction when it occurs.

---

## Refactoring and simplification require intentional triggering

Default tendency is to accumulate technical debt quietly rather than flag or fix it. Common anti-patterns observed:

- Introducing global variables where function parameters would be cleaner and more testable
- Duplicating logic across similar functions rather than extracting a shared utility
- Letting design drift accumulate across sessions — each session makes a locally reasonable choice that compounds into a messier architecture over time

This is especially noticeable when a task spans multiple sessions: each session picks up from a working state and adds the minimal change to move forward, without stepping back to look at the whole.

**Practical fixes worth exploring:**
- Adding a refactor checkpoint to architecture docs — a living list of known debt candidates
- Integrating a simplification review into the session-close checklist alongside `/summarise-session`
- Being explicit in `CLAUDE.md` about preferred patterns (e.g. "prefer function parameters over module-level globals in Python scripts")
- Exploring `/simplify` — a built-in Claude Code skill that runs a cleanup pass on changed code looking for reuse, simplification, and efficiency improvements. Worth testing as a lightweight forcing function after each logical change

**Status:** Active area. The cross-session drift problem in particular warrants more systematic investigation — recording known debt and expected patterns explicitly in architecture docs seems most promising.

---

## Skills as orchestration layer for personal productivity apps

Emerging pattern: using Claude Code skills as the primary orchestration layer for personal productivity systems works well, particularly when:

- The workflow involves multiple steps with human decision points (confirm a value, choose whether to run a follow-on step)
- Outputs are markdown files that double as logs — easy to review, version-control, and pass to future sessions
- The same skill can be triggered manually in a session or wired into a recurring schedule

**Strengths:**
- Low overhead to add new skills once the pattern is established
- Markdown outputs are human-readable and session-portable
- Natural fit for scheduling (skill as the scheduled job entrypoint)

**Fragility points still to resolve:**
- Failure modes are not well-handled: if a step fails mid-skill (e.g. a scrape returns nothing, a DB insert errors), there is no structured recovery path — it surfaces an error and stops, potentially leaving partial state
- No retries, rollbacks, or structured error logging within a skill run
- Skill definitions are markdown prose — no type-checking or linting, so schema drift (e.g. a DB column rename) breaks silently until the skill is run

**Status:** Pattern is productive for interactive use. For fully autonomous/scheduled runs, failure handling needs more thought before relying on it for higher-stakes operations.

---

## Artifact-design skill for internal technical communication

Seen some promising early results using the `/artifact-design` skill for generating artifacts to communicate technical concepts and decisions internally — architecture overviews, decision summaries, and similar.

Worth experimenting further with more interactive communication techniques and methods to see how far this pattern can stretch.

---

## claude-api skill dumps all language docs when it can't infer the project language

The `/claude-api` skill normally loads just one language's reference doc based on the project's source files. In a repo with no `.py`/`.ts`/etc. source files yet, it couldn't infer a language — and instead of asking which language to show, it fell through to injecting the entire "Included Documentation" block: every language's `claude-api/README.md`, `tool-use.md`, `streaming.md`, `batches.md`, `files-api.md`, plus all `managed-agents-*.md`, `model-migration.md`, `platform-availability.md`, etc. Roughly 10 languages × several files, ~309k tokens in one tool result.

**Root cause:** the "language can't be inferred" fallback is supposed to prompt the user to pick a language, but the tool-result content included everything regardless — a known rough edge in the skill.

**Practical implication:** in a repo without source files yet (e.g. very early in a project, before any code is written), state the intended language explicitly before invoking `/claude-api`, or skip re-invoking the skill and just proceed with that language's conventions directly.

---

## Custom subagents + skill orchestration for parallel research

Early encouraging result using [project-scoped subagents](https://code.claude.com/docs/en/sub-agents): a `.claude/agents/` subagent restricted to read-only tools, fanned out in parallel for the research/lookup portion of a batch task, with a skill orchestrating the fan-out and then performing any writes itself, serially, afterward.

Splitting on read-vs-write (rather than one full-flow agent per item) avoided race conditions between concurrent items while still parallelizing the actual bottleneck. Restricting the subagent's `tools:` frontmatter made the "read-only" boundary structural rather than just a prompt instruction.

**Status:** One small-N test so far — promising, worth reusing for similar batch workflows.

---

## Spec-driven development pays off mainly in fully autonomous mode

The expectation was that a highly detailed spec (e.g. produced by `/grill-me`) would drive significant efficiency gains by front-loading decisions before the build starts. In practice this only holds up when working in a fully autonomous pattern, where the agent runs the spec through to completion without a human steering mid-build.

In a more co-pilot/steward style of working — where the intent is to stay closely involved in agentic engineering rather than hand off and check back later — the detailed spec doesn't produce the same payoff. Points from the spec end up getting re-debated and expanded live during the build anyway, particularly when part of the goal is to let design thinking develop *as* the build progresses rather than be fully resolved up front. The upfront spec-writing cost is paid, but the expected downstream efficiency doesn't materialise, because the human is intentionally still in the design loop.

A related, separate cost: keeping documentation (specs, ADRs, architecture docs) consistent with the code over time has a real ongoing cost even with agent help — it doesn't fully disappear just because an agent can update docs quickly. This points to context management as the area to improve, rather than spec detail: e.g. lighter-weight specs for co-pilot sessions, deferring heavy spec investment to genuinely autonomous runs, and finding ways to keep an agent's working context current without a full documentation-sync pass every session.

**Practical implication:** Match spec detail to work mode. For fully autonomous sessions, invest in a detailed `/grill-me`-style spec upfront. For co-pilot/steward sessions where design is meant to evolve during the build, a lighter spec is likely sufficient — the heavy detail will be renegotiated live regardless.

**Status:** Open area. Worth experimenting with lighter-weight spec formats for co-pilot sessions, and with context management approaches (e.g. incremental context updates vs. full doc re-sync) that reduce the ongoing cost of keeping documentation current.

---

## OpenRouter routing doesn't guarantee lowest price unless you set a strategy

Routing a model request through OpenRouter does not mean you get the lowest price quoted for that model, or any specific provider, by default. The default behaviour appears to balance low price against high uptime — so OpenRouter may route to a more expensive or more available provider rather than the cheapest one.

**Practical implication:** If you want bounded or predictable cost, set the routing strategy (provider preferences / sort order) explicitly on the request rather than relying on the default. Don't assume "via OpenRouter" equals "cheapest available".

---

## Claude Code web AI workflows need extra infra setup

Getting a Claude Code web AI workflow to run effectively takes more than pointing it at a repo. Components that needed to be set up separately:

- **A separate API key** for any external service the workflow calls (e.g. OpenRouter) — this is not inherited from local config and must be provided to the web environment.
- **An allowed-domains list** — outbound network access is restricted, so any host the workflow needs to reach (e.g. `openrouter.ai`) must be explicitly allow-listed.
- **Awareness that the local Claude CLI can push workflows back to the cloud once started** — this appears to be new functionality. Be intentional about whether you want a workflow to hand off to the cloud mid-run; it can happen without you explicitly asking for it.

**Practical implication:** Budget setup time for keys and domain allow-listing before expecting a web workflow to work end-to-end, and decide up front whether local→cloud handoff is desired for a given workflow.

**Related:** [[Visual browser review is not available in Claude Code web]], [[Granting Claude Code web access to a private GitHub repo]]
