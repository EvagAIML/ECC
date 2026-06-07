# EVSD — Evaluation & Overview: Everything Claude Code (ECC)

**Document type:** EVSD (Evaluation & Overview)
**Subject:** `everything-claude-code` / `ecc-universal`, vendored here as a fork of [`affaan-m/ECC`](https://github.com/affaan-m/ECC)
**Upstream version evaluated:** `2.0.0-rc.1`
**Evaluated:** 2026-06-06
**Author of ECC:** Affaan Mustafa (MIT license, [ecc.tools](https://ecc.tools))

---

## At a glance

| Property | Value |
|---|---|
| What it is | A cross-harness "agent operating system" — a pack of agents, skills, commands, hooks, and rules that upgrades AI coding tools |
| Size (rc.1) | 63 agents · 251 skills · 79 commands · 4 hook families · 60+ rule files · 14 MCP configs |
| Works with | Claude Code, OpenAI Codex, Cursor, OpenCode, Gemini, Zed, GitHub Copilot, and terminal workflows |
| Install model | 5 profiles — `core`, `developer`, `security`, `research`, `full` |
| Adoption | ~82K GitHub stars, ~10.7K forks; the most-starred Claude Code config repo |
| License | MIT |

---

## 1. What it does — the simple version

Out of the box, an AI coding assistant is a generalist. It knows a lot but follows no particular process, and it forgets what worked last time.

ECC is a large bundle of pre-written instructions that turns that generalist into a disciplined team. You install it once, and your coding assistant gains:

- **Specialists ("agents")** it can hand work to — a planner, a code reviewer, a security reviewer, language-specific reviewers (Python, Go, Rust, TypeScript, Kotlin, and more), build-error fixers, and a test-driven-development guide.
- **Recipes ("skills")** — 250+ reusable workflows for things like writing tests first, reviewing security, designing an API, building a marketing campaign, or producing a slide deck.
- **Shortcuts ("commands")** — slash commands like `/tdd`, `/code-review`, `/plan`, `/loop-start` that trigger those recipes.
- **Guardrails ("hooks")** — automatic checks that fire on every action: block dangerous shell commands, auto-format edited files, track cost, and stop a session if work is uncommitted.
- **Memory ("instincts" / continuous learning)** — it extracts what worked from each session and reuses it next time, so the setup gets better with use.

In one sentence: **ECC makes an AI coding assistant act like a senior engineering team with a process, a memory, and safety rails — across whichever AI tool you happen to use.**

---

## 2. What it can be used for

The five install profiles map cleanly to five kinds of value:

- **`developer` — everyday engineering.** Plan a feature, build it test-first, get it reviewed, fix the build, clean dead code. The core daily loop.
- **`security` — audits and hardening.** Review code for vulnerabilities, and audit *AI agent configs themselves* (CLAUDE.md, `.cursorrules`, `agents.json`) for prompt-injection and data-leak holes via the red-team/blue-team "AgentShield" pipeline.
- **`research` — investigation and publishing.** Market research, investor materials, content engines, cross-posting — synthesis work, not just code.
- **`core` — minimal trial.** Commands + core agents + hooks, smallest footprint.
- **`full` — power use.** All 18 modules, every agent and skill.

Concretely, the high-value jobs it is good at:

1. **Autonomous feature development** with tests written first and enforced coverage.
2. **Code and security review** that filters noise (only reports issues it is >80% confident are real).
3. **Multi-agent orchestration** — split a task across parallel planners/workers, then merge.
4. **Self-improving setup** — capture reusable patterns from your own git history and sessions into skills.
5. **Cross-tool consistency** — the same process and rules whether you are in Claude Code, Cursor, or Codex.
6. **Non-code workflows** — marketing campaigns, slide decks, animated explainer videos (manim), SEO.

### Why this is directly relevant here

Three fits with the current workspace stand out:

- **Audit our own CLAUDE.md.** This workspace runs on a very large `CLAUDE.md` plus many standards files. ECC's security profile is purpose-built to red-team exactly that kind of agent config and return a scored report — a high-value, low-effort first run.
- **Codify our notebook standards as skills.** ECC's `/skill-create` reads git history and turns recurring patterns into `SKILL.md` files. Pointed at the gated-pipeline repos, it can capture the Colab-replicable notebook discipline into reusable, enforceable skills.
- **A real multi-agent pipeline.** The hub/dispatch model already plans-then-delegates. ECC's `planner → tdd-guide → code-reviewer → security-reviewer → learn` chain is a concrete, installable version of that delegation principle.

---

## 3. Deep evaluation

### Architecture

ECC is fundamentally a **configuration distribution system**, not a runtime. It ships markdown definitions (agents, skills, commands, rules), small Node.js scripts (the `ecc` CLI, install planner/applier, control pane), JSON schemas that validate the catalog, and harness adapters that translate one shared identity into each tool's native format (`.claude/`, `.codex/`, `.cursor/`, `.gemini/`, `.opencode/`, `.zed/`, and others). An install planner resolves a profile into modules and writes them into the target harness's config directories.

Quality signals from reading the source:

- **Agent definitions are disciplined.** Each agent carries a "Prompt Defense Baseline" (resists role-override, refuses to leak secrets, treats fetched/untrusted content as suspicious), an explicit tool allowlist, a model assignment, and confidence-based output filtering. This is materially better than the typical "you are an expert" one-liner agent.
- **Hooks are a real deterministic safety layer**, not decoration — dangerous-command blocking, auto-format, cost tracking, and session governance, with runtime gating (`ECC_HOOK_PROFILE=minimal|standard|strict`) so you can dial the strictness without editing files.
- **It is self-documenting and tested** — a catalog checker, a command registry generator, ~58 test files, and JSON schema validators keep the large surface coherent.
- **Continuous learning is versioned** (v1 deprecated in favor of an instinct-based v2 with confidence scoring and project-scoped storage), which signals active, thoughtful maintenance rather than a dumped config.

### Strengths

- Breadth and depth are genuinely large, and the structure holds together.
- Security-first posture is baked into the agents, not bolted on.
- Cross-harness portability is rare and valuable if you use more than one AI tool.
- The learning loop means the value compounds over time.

### Caveats and risks (no sugar-coating)

- **It is large, and large is not free.** 60+ agents and 250+ skills add surface area and can consume context budget. The author's own framing has shifted to a "performance optimization system," and the project is openly described in coverage as *dividing* the developer community — some practitioners consider this degree of scaffolding over-engineering versus a lean handful of agents. Start with `core` or `developer`, not `full`.
- **Hooks execute shell commands and the installer writes into your config directories.** This is a third-party pack (vendored here as a fork). It is MIT and widely used, but treat installation as running someone else's automation on your machine: read the hooks, install a narrow profile first, and keep `ECC_HOOK_PROFILE` conservative until trusted.
- **It is a release candidate (`2.0.0-rc.1`).** Expect churn; pin to a commit for anything you depend on.
- **"Agents/skills" are prompts, not guarantees.** They raise the floor on quality and consistency; they do not remove the need for human review of important changes.

### Verdict

A high-quality, actively maintained, security-conscious agent configuration system that is worth adopting **selectively**. The right move is a narrow install plus two or three targeted runs (below), not a full install on day one.

---

## 4. Turning ECC into high-value solutions with simple prompts

The pattern is the same every time, which is what makes it automatable:

> **Install a profile once → invoke a skill/command with a one-line prompt → let the agent chain run → capture what worked as a new skill.**

Because the heavy logic lives inside the skills and agents, the *prompt you type stays short*. The complexity is pre-loaded. That is the whole point: a one-line prompt detonates a multi-step, multi-agent workflow.

### 4a. High-value test examples (each is a single, simple prompt)

Run these in a harness (Claude Code, Cursor, or Codex) after installing the matching profile. Each is chosen to produce outsized value from minimal input.

| # | Profile | One-shot prompt to run | Why it is high value |
|---|---|---|---|
| 1 | `security` | `Run a red-team/blue-team security audit on this repo's CLAUDE.md and all standards/*.md files and give me a scored report with concrete fixes.` | Hardens our own large agent config against prompt-injection — direct, immediate risk reduction. |
| 2 | `developer` | `/skill-create — analyze this repo's git history and generate SKILL.md files for our recurring patterns.` | Converts tribal knowledge (our notebook + pipeline discipline) into reusable, enforceable skills. |
| 3 | `developer` | `/tdd Add input validation and 90% test coverage to <module>, tests first.` | Autonomous, test-first feature work with coverage enforced by the tdd-guide agent. |
| 4 | `developer` | `/multi-plan then /multi-execute: refactor <service> to add pagination and caching, no breaking changes.` | Multi-model plan → parallel execution → merge; senior-level planning on a one-liner. |
| 5 | `developer` | `/loop-start: fix every failing test in this repo until the suite is green, stop if you stall.` | Hands-off remediation with the loop-operator's stall detection and safety stops. |
| 6 | `core` | `Generate a codemap and architecture overview of this repository, then list the five riskiest files.` | Instant onboarding/orientation on an unfamiliar codebase. |
| 7 | `research` | `/marketing-campaign: <one-paragraph product brief>` → positioning, landing copy, email sequence, social posts, ad variants, content calendar. | A full content suite from a paragraph — relevant to product/portfolio launches. |
| 8 | `developer` | `/model-route — analyze my last sessions and route each task type to the cheapest model that meets quality.` | Direct cost reduction on token spend with no quality loss on routine work. |

The four to run first, in order, given this workspace: **#1 (audit our config), #2 (codify our standards), #6 (codemap a repo), #3 (a real TDD task).** Those produce value with essentially zero risk and validate the install.

### 4b. A repeatable automation process

This wraps the prompts above into a standing pipeline that fits the hub/dispatch model already in use.

**Stage 0 — Provision (once per machine/harness).**
Install a narrow profile and verify it loads.
```bash
node scripts/install-plan.js --profile developer && node scripts/install-apply.js
```
Keep hooks conservative to start: `export ECC_HOOK_PROFILE=standard`.

**Stage 1 — Trigger.** A task enters via a dispatch spec, a scheduled job, or a chat request. The trigger carries one line of intent (the "simple prompt").

**Stage 2 — Plan.** Route to the `planner` (or `/multi-plan` for non-trivial work). Output: a step list, no production changes yet. Gate: a human or a lead agent approves the plan for anything irreversible.

**Stage 3 — Execute test-first.** `/tdd` drives the change with tests before implementation; `build-error-resolver` clears failures; `/loop-start` handles long remediation with stall detection.

**Stage 4 — Review (always).** `code-reviewer` then `security-reviewer` run automatically. Only >80%-confidence issues surface. Critical findings block the merge.

**Stage 5 — Learn.** `/learn-eval` extracts reusable patterns from the session, self-scores them, and saves them as project or global skills/instincts. The setup is now better than it was at Stage 0.

**Stage 6 — Report.** Emit a short result (what changed, tests, review findings, new skills captured) to the outbox / status channel.

Hooks run across all stages as the deterministic floor: dangerous commands blocked, files auto-formatted, cost tracked, sessions not allowed to end dirty.

**Prompts that drive each stage (copy-paste, one line each):**

```
Plan:    Plan the implementation of <goal> as discrete steps; do not modify production code.
Execute: /tdd Implement the approved plan for <goal>, tests first, target 85% coverage.
Fix:     /loop-start Resolve all build and test failures for <goal> until green; stop if you stall.
Review:  Run code-reviewer then security-reviewer on the diff; block on any CRITICAL finding.
Learn:   /learn-eval Extract reusable patterns from this session and save them with a quality gate.
Report:  Summarize what changed, test results, review findings, and any new skills captured.
```

A fully automated single-prompt version, for a trusted task:

```
Take <goal>: plan it, build it test-first to 85% coverage, fix the build, run code-reviewer and
security-reviewer, then /learn-eval to capture what worked. Stop and ask me only before an
irreversible action.
```

---

## 5. Provenance & naming

- **Source:** [`github.com/affaan-m/ECC`](https://github.com/affaan-m/ECC) (MIT). Vendored into this account as a fork so full history and all files (~2,900) are preserved.
- **Naming alignment:** per workspace convention, the canonical repo name is `000-ecc-everything-claude-code` (the `000-` prefix, with the name stating what it is). The fork currently sits at its inherited name and needs a one-step rename in repo settings to land on the convention (see the handoff notes accompanying this document).
- **This document** follows the workspace markdown standard (Style B, structured technical) and the public-artifact voice rules.

---

## Sources

- ECC repository: https://github.com/affaan-m/ECC
- ECC project site: https://ecc.tools
- "Everything Claude Code: Inside the 82K-Star Agent Harness" — Medium (Ewan Mak): https://medium.com/@tentenco/everything-claude-code-inside-the-82k-star-agent-harness-thats-dividing-the-developer-community-4fe54feccbc1
- "Everything Claude Code (ECC): production engineering platform" — SitePoint: https://www.sitepoint.com/everything-claude-code-ecc-production-engineering-platform/
- ClaudePluginHub listing: https://www.claudepluginhub.com/plugins/affaan-m-everything-claude-code
