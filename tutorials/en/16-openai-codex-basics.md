# Lesson 16: OpenAI Codex Basics and Practical Workflows

> Learning objective: Understand OpenAI Codex surfaces, installation and auth, safety boundaries, project configuration, and common development workflows.
>
> Current baseline: 2026-06-20. This chapter is based on the OpenAI Codex manual and applies to Codex CLI, IDE extension, Codex app, Codex cloud, and GitHub code review workflows.

## 1. What Is Codex?

Codex is OpenAI's coding agent for software development. It can help you:

- Write code that follows your existing project structure and conventions.
- Understand unfamiliar or legacy codebases.
- Review code for bugs, edge cases, and risky changes.
- Debug failures by reading logs, reproducing issues, and tracing root causes.
- Automate development tasks such as refactoring, testing, migrations, documentation, and release preparation.

Unlike a plain chatbot, Codex can work inside a real repository, read context, edit files, run commands, and use sandboxing and approvals to manage risk.

## 2. Codex and Claude Code

This repository previously focused mostly on Claude Code. Going forward, treat Claude Code and Codex as two parallel AI coding tools.

| Dimension | Claude Code | OpenAI Codex |
|-----------|-------------|--------------|
| Main surfaces | Claude Code CLI, IDE, MCP, subagents | Codex CLI, IDE extension, Codex app, Cloud, GitHub |
| Persistent guidance | `CLAUDE.md`, project config, skills | `AGENTS.md`, `config.toml`, skills, plugins |
| Tool extension | MCP, hooks, skills, plugins | MCP, skills, plugins, rules, hooks |
| Safety model | Tool permissions, approvals, sandbox/environment controls | Sandbox mode, approval policy, rules, approval flow |
| Best fit | Claude ecosystem, long-context collaboration, Claude Code workflows | OpenAI ecosystem, multi-surface Codex workflows, GitHub review, cloud tasks |

Learning path:

- If you already know Claude Code, start from sections 4, 5, and 6 to learn Codex setup, safety, and workflow differences.
- If you are new to AI coding agents, start with the CLI + `AGENTS.md` + sandbox loop in this chapter.

## 3. Codex Surfaces

Codex is not a single interface. It includes several surfaces that can work together.

### 3.1 Codex CLI

Best for local repository work, terminal workflows, and automation.

Common commands:

```bash
# Launch the interactive terminal UI
codex

# Start in a specific directory
codex --cd /path/to/project

# Use a low-risk local development setup
codex --sandbox workspace-write --ask-for-approval on-request

# Run one non-interactive task
codex exec "Review this repository and list the highest-risk issues"

# Diagnose local install, config, auth, and runtime issues
codex doctor
```

### 3.2 Codex IDE Extension

Best for working with code directly in your editor. Use it to:

- Ask about the current file or selected code.
- Make local edits with visible diffs.
- Reuse the same `config.toml`, MCP servers, and project guidance.
- Keep the agent close to the files you are editing.

### 3.3 Codex App

The Codex app is useful for richer local collaboration: planning, review, browser testing, thread management, and workspace-level work.

### 3.4 Codex Cloud

Codex cloud is useful for offloading and parallelizing work:

- Fixing GitHub issues
- Addressing PR comments
- Comparing multiple implementation attempts
- Running longer tasks without tying up your local machine

CLI examples:

```bash
codex cloud
codex cloud list --json
codex cloud exec --env <ENV_ID> "Fix the failing tests"
```

### 3.5 GitHub Code Review

After configuring Codex cloud and GitHub integration, ask for review in a pull request:

```text
@codex review
```

Codex reviews the PR diff like a teammate and focuses on high-priority issues. You can then ask it to fix a finding:

```text
@codex fix the P1 issue
```

## 4. Install and Authenticate

### 4.1 Install

Installation can change over time, so check the official Codex installation docs first. After installation, verify:

```bash
codex --version
codex doctor
```

### 4.2 Login

Codex supports ChatGPT login, device auth, API key auth, and access-token auth. The common flow is:

```bash
codex login
```

For automation or CI, API key auth can be useful. Keep the boundary clear: API key auth usually covers CLI, SDK, or IDE extension use. It does not automatically grant ChatGPT account features, cloud tasks, Slack, or GitHub review access.

## 5. First Safe Workflow

Start in a test repository:

```bash
mkdir codex-demo
cd codex-demo
git init
codex --sandbox workspace-write --ask-for-approval on-request
```

Prompt:

```text
Create a small Node.js CLI that reads a JSON file and prints a summary.
Use tests. Explain every command before you run it.
```

Watch whether Codex:

1. Reads the repository structure first.
2. Proposes a plan.
3. Writes only inside the workspace.
4. Asks before crossing safety boundaries.
5. Runs tests and reports the result.

## 6. `AGENTS.md`: Project Guidance for Codex

`AGENTS.md` is persistent project guidance for Codex. Use it for team conventions, not one-off tasks.

Create one at the repository root:

```md
# AGENTS.md

## Repository expectations

- Use concise commits with clear scopes.
- Run `npm test` after changing JavaScript or TypeScript files.
- Do not add production dependencies without explaining why.
- Keep documentation in Chinese and English synchronized when changing tutorial indexes.

## Review guidelines

- Prioritize bugs, security risks, breaking changes, and missing tests.
- Treat broken internal documentation links as P1 in tutorial files.
```

Codex reads global and project guidance. Files closer to the current working directory, including `AGENTS.md` or `AGENTS.override.md`, override broader guidance.

Good content for `AGENTS.md`:

- Build, test, and lint commands
- Code style and naming conventions
- Pull request review focus
- Security boundaries
- Documentation sync rules
- Subdirectory-specific conventions

Avoid putting these in `AGENTS.md`:

- One-off requirements
- Secrets, tokens, or passwords
- Long product documents
- Issue details that change frequently

## 7. Sandboxing and Approvals

Codex safety has two main controls:

- Sandbox: the technical boundary for file access, network access, and command execution.
- Approval policy: when Codex pauses for human confirmation.

Common sandbox modes:

| Mode | Meaning | Use case |
|------|---------|----------|
| `read-only` | Inspect files only; no direct edits or side-effecting commands | Review and analysis |
| `workspace-write` | Edit inside the workspace and run routine commands | Daily development, recommended start |
| `danger-full-access` | Remove sandbox restrictions | Only in isolated environments |

Common approval policies:

| Policy | Meaning |
|--------|---------|
| `untrusted` | Ask before commands outside the trusted set |
| `on-request` | Work inside the sandbox and ask before crossing boundaries |
| `never` | Do not show approval prompts |

Recommended default:

```bash
codex --sandbox workspace-write --ask-for-approval on-request
```

Only consider full bypass inside an externally isolated container, temporary VM, or CI sandbox:

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

## 8. `config.toml` Basics

Codex local configuration usually lives at:

```text
~/.codex/config.toml
```

Trusted projects can also use:

```text
.codex/config.toml
```

Example:

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"

[sandbox_workspace_write]
writable_roots = ["."]
```

Use `-c key=value` to override config for one invocation:

```bash
codex -c sandbox_mode=\"read-only\" "Review this repository for risky changes"
```

## 9. MCP: Connect External Tools

Codex supports MCP for connecting documentation, browsers, Figma, GitHub, Sentry, Linear, and internal systems.

Add an MCP server with the CLI:

```bash
codex mcp add context7 -- npx -y @upstash/context7-mcp
codex mcp --help
```

Check active servers in the TUI:

```text
/mcp
```

Or configure `config.toml` directly:

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
```

MCP works best with skills:

- A skill defines the workflow.
- MCP provides external tools and context.
- `AGENTS.md` keeps repository conventions durable.

## 10. Skills and Plugins

Codex skills package reusable workflows. A skill is usually a directory with `SKILL.md`, and it may include scripts, references, and assets.

Minimal example:

```md
---
name: docs-sync
description: Use when updating bilingual tutorial docs, README files, indexes, or changelog entries.
---

1. Update Chinese and English docs together.
2. Check all relative Markdown links.
3. Record version-sensitive changes in CHANGELOG.md.
4. Keep terminology consistent across README.md and README_EN.md.
```

Common locations:

| Scope | Location | Use |
|-------|----------|-----|
| User | `$HOME/.agents/skills` | Personal workflows across repositories |
| Repository | `.agents/skills` | Team workflows checked into a repo |
| Plugin | plugin package | Distribution of skills, MCP config, and integrations |

Plugins are installable distribution units. They can bundle multiple skills, MCP server config, app integrations, and assets. Start with a local skill, then package it as a plugin when the workflow is stable.

## 11. Common Codex Workflows

### 11.1 Explore an Unfamiliar Repository

```text
Read this repository and summarize:
1. main modules
2. build and test commands
3. likely risk areas
4. where to start for a bug fix
Do not edit files.
```

Use a `read-only` sandbox.

### 11.2 Implement a Feature

```text
Add search filtering to the task list.
First inspect the current data flow.
Then propose a plan.
After approval, implement and run tests.
```

### 11.3 Fix a Bug

```text
The test `task-filter.test.ts` fails.
Reproduce the failure, identify the root cause, make the smallest fix, and run the relevant tests.
```

### 11.4 Review Code

```bash
codex -c sandbox_mode=\"read-only\" "Review the current git diff. Prioritize correctness, security, regressions, and missing tests."
```

### 11.5 Sync Documentation

```text
Update the Chinese and English README indexes after adding a new tutorial.
Then check all relative Markdown links.
```

## 12. Team Adoption

For team rollout, build in this order:

1. Add root `AGENTS.md` with test commands, style rules, and review expectations.
2. Add lint, test, and typecheck commands so Codex has executable feedback.
3. Create skills for repeated workflows: review, release checks, docs sync, migrations.
4. Use MCP for external systems: GitHub, Linear, Figma, Sentry, internal docs.
5. Enable Codex review on GitHub PRs for high-signal review coverage.
6. Use stricter sandbox, approval, and rules settings for high-risk repositories.

## 13. Common Mistakes

- Treating Codex like a chatbot instead of making it read, edit, test, and verify.
- Skipping project guidance, which makes Codex guess team conventions.
- Giving full access too early. Prefer `workspace-write + on-request` for daily work.
- Looking only at generated code instead of reviewing diffs, tests, logs, and findings.
- Putting every rule in prompts. Stable rules belong in `AGENTS.md` or a skill.
- Forgetting bilingual documentation sync in tutorial repositories.

## 14. How This Repository Should Expand

Future Codex lessons should cover:

- Lesson 17: Codex CLI deep dive
- Lesson 18: `AGENTS.md`, skills, and plugins in practice
- Lesson 19: Codex + GitHub Review + Cloud tasks
- Lesson 20: Comparing and migrating Claude Code and Codex workflows

---

📚 [Back to Course Directory](../../README_EN.md)
