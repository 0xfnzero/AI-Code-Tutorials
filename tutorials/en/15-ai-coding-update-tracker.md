# Lesson 15: AI Coding Update Tracker and Iteration Playbook

> Learning objective: Build a repeatable update process so this tutorial can keep pace with Claude Code, MCP, Skills, Agent SDK, and modern AI-assisted development workflows.
>
> Current baseline: 2026-06-20. The Claude Code npm latest version is `2.1.183`, and the official changelog entry is dated 2026-06-19. The latest stable MCP specification version is `2025-11-25`.

## 1. Why Continuous Tracking Matters

AI coding tools now evolve faster than traditional tutorial maintenance cycles. A guide that used to stay accurate for half a year can now become stale in weeks as CLI commands, configuration fields, model names, permission systems, MCP protocol details, and agent workflows change.

This repository should follow one rule going forward: **teach durable methods, and keep version-specific details easy to verify**.

- Durable content: workflows, task decomposition, code review, testing, safety boundaries, and engineering judgment.
- Content that needs regular checks: CLI flags, model names, config fields, MCP server packages, deployment platform steps, pricing, and quotas.
- Content that must cite sources: official commands, protocol specifications, security guidance, model capabilities, and release dates.

## 2. Monthly Update Checklist

Run a light update pass every month and a broader review every quarter.

### 2.1 Claude Code

Sources to check:

- [Claude Code Changelog](https://code.claude.com/docs/en/changelog)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)
- npm package: `npm view @anthropic-ai/claude-code version dist-tags time.modified --json`

Checklist:

- Latest version and release date
- New or deprecated CLI commands
- Permission, safety, auto mode, and Git protection changes
- Changes to subagents, skills, plugins, hooks, IDE integration, and background tasks
- Windows, macOS, and Linux compatibility updates

### 2.2 MCP

Sources to check:

- [MCP official docs](https://modelcontextprotocol.io/docs/getting-started/intro)
- [Latest MCP specification](https://modelcontextprotocol.io/specification/latest)
- [MCP specification changelog](https://modelcontextprotocol.io/specification/2025-11-25/changelog)

Checklist:

- Current specification version
- Changes to tools, resources, prompts, sampling, and elicitation
- OAuth, OpenID Connect, and scope consent changes
- Server registry, icons, tool name guidance, and ecosystem metadata
- Production security expectations: least privilege, audit logs, tool isolation, and user confirmation

### 2.3 Skills, Plugins, and Reusable Workflows

Sources to check:

- [Claude Code Skills docs](https://code.claude.com/docs/en/skills)
- [Agent Skills overview](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

Checklist:

- Whether custom commands have moved into the Skills workflow
- Changes to `.claude/skills/*/SKILL.md` frontmatter
- New bundled skills for code review, debugging, running, or verification
- How skills combine with subagents, MCP, and dynamic context injection
- Security review requirements for third-party skills

### 2.4 Agent SDK and Production Agents

Source to check:

- [Claude Agent SDK docs](https://code.claude.com/docs/en/agent-sdk/overview)

Checklist:

- TypeScript and Python SDK installation steps
- New agent loop, permission, hook, checkpoint, and observability features
- Whether tutorial prompt workflows should become runnable agents
- Production needs such as queues, sandboxes, logs, cost tracking, and human approval

## 3. 2026 Topics to Add or Expand

### 3.1 From Prompt Engineering to Context Engineering

Prompting still matters, but AI coding increasingly depends on context engineering:

- Project rules: `CLAUDE.md`, `AGENTS.md`, architecture notes, and naming conventions
- Executable context: test, lint, startup, and migration commands
- Task context: acceptance criteria, failing examples, screenshots, logs, and reproduction steps
- Tool context: MCP, browser automation, databases, CI, monitoring, and design tools

Lessons 7, 9, 12, and 14 should gradually add dedicated sections on structuring context.

### 3.2 Skills and Project-Level Workflows

Many copy-paste prompt templates in older lessons can become Skills:

- `/code-review`: fixed review scope, output format, and risk levels
- `/debug`: fixed reproduction, hypothesis, experiment, and verification flow
- `/release-check`: fixed test, build, changelog, and security checks
- `/docs-update`: fixed bilingual sync, link checks, and terminology rules

A future lesson should cover `.claude/skills/`, `SKILL.md`, and team sharing patterns.

### 3.3 MCP Production Security

MCP is no longer just an introductory way to connect AI to tools. Production setups need:

- Clear permission boundaries for every MCP server
- Audit logs before and after tool calls
- Human confirmation for high-risk tools
- Prompt injection defenses for external content
- Minimal exposure of tokens, cookies, and database connection strings
- Stable server names, tool names, and parameter schemas

Lesson 13 should evolve from "make it run" to "make it safe to operate."

### 3.4 Multi-Agent Verification

The value of multi-agent work is not having more roles. It comes from parallel exploration and cross-checking.

- One agent implements while another reviews
- One agent analyzes requirements while another writes tests
- Long tasks are split into small, verifiable units
- Subagent results return to the main flow for integration and verification

## 4. Content Update Workflow

For each update:

1. Pick a theme: CLI, MCP, Skills, Agent SDK, hands-on projects, security, or deployment.
2. Read official sources and record exact dates, versions, and links.
3. Search the repository for stale content such as `2025`, old model names, old commands, and old deployment platforms.
4. Update Chinese and English content together so both tables of contents stay aligned.
5. For command references, verify with `--help`, `npm view`, or official docs.
6. Record the update in `CHANGELOG.md` with date, source, and affected files.
7. If something is a trend judgment rather than a confirmed fact, label it as an observation or recommendation.

## 5. Repository Roadmap

### P0: Fix Clearly Stale Wording

- Replace "latest January 2025" wording with maintenance status and verification dates
- Fix the English README course count
- Add Lesson 15 and a continuous update entry point to both READMEs

### P1: Cover Modern Claude Code Capabilities

- Add a dedicated Skills topic
- Update subagent and nested subagent practices
- Add hooks, permissions, safe mode, and checkpoint scenarios
- Cover IDE, GitHub, background task, and team collaboration workflows

### P2: Upgrade the MCP Lessons

- Cover tools, resources, prompts, sampling, and elicitation using the latest MCP specification
- Add OAuth, scope consent, and safety boundaries
- Separate local learning configuration from production configuration

### P3: Refresh Hands-On Deployment Content

- Expand Heroku-only deployment into Render, Railway, Fly.io, Vercel, and Cloudflare Pages/Workers comparisons
- Add environment variables, migrations, logging, monitoring, and rollback
- Add AI-assisted testing, screenshot verification, and CI review workflows

## 6. Long-Term Learning Advice

- Practice with a real project every week instead of only collecting prompts.
- Review monthly which tasks fit agents and which tasks still need human design.
- Keep failure cases: wrong edits, deleted files, misunderstood requirements, and missed tests are valuable training material.
- Keep engineering verification in the loop: tests, type checks, code review, security scanning, and manual acceptance all still matter.

---

📚 [Back to Course Directory](../../README_EN.md)
