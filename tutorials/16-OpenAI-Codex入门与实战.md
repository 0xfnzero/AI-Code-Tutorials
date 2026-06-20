# 第十六课：OpenAI Codex 入门与实战

> 学习目标：系统了解 OpenAI Codex 的产品形态、安装认证、安全边界、项目配置和常见开发工作流。
>
> 当前基线：2026-06-20。本章依据 OpenAI Codex 官方 manual 更新，适用于 Codex CLI、IDE 扩展、Codex app、Codex cloud 和 GitHub code review 等场景。

## 1. Codex 是什么

Codex 是 OpenAI 面向软件开发的 coding agent。它可以帮助你：

- 写代码：根据需求生成符合项目结构和约定的代码。
- 理解代码库：阅读陌生项目，解释模块关系和关键流程。
- 审查代码：检查 bug、边界条件、逻辑错误和高风险改动。
- 调试问题：分析失败日志、复现路径和根因。
- 自动化开发任务：执行重构、测试、迁移、文档更新和发布准备。

与普通聊天机器人不同，Codex 的核心价值在于它可以进入真实代码仓库，读取上下文、编辑文件、运行命令，并通过沙箱和审批机制控制风险。

## 2. Codex 与 Claude Code 的关系

本仓库之前以 Claude Code 为主。后续建议把两者作为并列工具学习：

| 维度 | Claude Code | OpenAI Codex |
|------|-------------|--------------|
| 核心入口 | Claude Code CLI、IDE、MCP、subagents | Codex CLI、IDE 扩展、Codex app、Cloud、GitHub |
| 持久指导 | `CLAUDE.md`、项目配置、skills | `AGENTS.md`、`config.toml`、skills、plugins |
| 工具扩展 | MCP、hooks、skills、plugins | MCP、skills、plugins、rules、hooks |
| 安全模型 | 工具权限、审批、沙箱/环境控制 | sandbox mode、approval policy、rules、审批流 |
| 适合场景 | Claude 生态、长上下文协作、Claude Code 工作流 | OpenAI 生态、Codex 多端协作、GitHub review、云任务 |

学习建议：

- 已经熟悉 Claude Code：从第 4、5、6 节开始，重点学习 Codex 的配置、安全和工作流差异。
- 第一次接触 AI coding agent：先按本章走一遍 CLI + `AGENTS.md` + sandbox 的最小闭环。

## 3. Codex 的主要入口

Codex 不是单一界面，而是一组协作入口。

### 3.1 Codex CLI

适合本地仓库开发、命令行工作流和自动化。

常见命令：

```bash
# 启动交互式终端 UI
codex

# 在指定目录启动
codex --cd /path/to/project

# 使用低风险本地开发配置
codex --sandbox workspace-write --ask-for-approval on-request

# 非交互运行一次任务
codex exec "Review this repository and list the highest-risk issues"

# 诊断本地安装、配置、认证和运行环境
codex doctor
```

### 3.2 Codex IDE 扩展

适合在编辑器里边看代码边和 agent 协作。IDE 扩展适合：

- 针对当前文件或选区提问
- 让 Codex 修改局部代码
- 在编辑器中查看 diff
- 复用同一套 `config.toml`、MCP 和项目指导

### 3.3 Codex App

Codex app 适合更完整的本地协作体验，例如规划、审查、浏览任务、使用 in-app browser、管理线程和工作区。

### 3.4 Codex Cloud

Codex cloud 适合把任务交给云端环境并行处理，例如：

- 修复 GitHub issue
- 处理 PR 评论
- 跑多个方案进行比较
- 在不占用本地机器的情况下执行长任务

CLI 里也可以浏览或提交云任务：

```bash
codex cloud
codex cloud list --json
codex cloud exec --env <ENV_ID> "Fix the failing tests"
```

### 3.5 GitHub Code Review

在配置 Codex cloud 和 GitHub 集成后，可以在 PR 中使用：

```text
@codex review
```

Codex 会像团队成员一样审查 PR diff，并重点报告高优先级问题。你也可以进一步请求：

```text
@codex fix the P1 issue
```

## 4. 安装与认证

### 4.1 安装

不同平台的安装方式会随版本变化，请优先参考官方安装说明。安装后先检查：

```bash
codex --version
codex doctor
```

### 4.2 登录

Codex 支持 ChatGPT 登录、设备认证、API key 或 access token。常用方式：

```bash
codex login
```

自动化或 CI 场景可以使用 API key，但要注意：API key 认证通常只覆盖 CLI、SDK 或 IDE 扩展能力，不等同于 ChatGPT 账号、云任务、Slack、GitHub review 等产品权限。

## 5. 第一个安全工作流

建议在一个测试仓库中开始：

```bash
mkdir codex-demo
cd codex-demo
git init
codex --sandbox workspace-write --ask-for-approval on-request
```

可以这样提问：

```text
Create a small Node.js CLI that reads a JSON file and prints a summary.
Use tests. Explain every command before you run it.
```

观察 Codex 的行为：

1. 是否先读取仓库结构。
2. 是否提出计划。
3. 是否只在工作区内写文件。
4. 是否在运行高风险命令前请求审批。
5. 是否运行测试并报告结果。

## 6. `AGENTS.md`：Codex 的项目说明书

`AGENTS.md` 是 Codex 的持久项目指导。它适合写团队约定，而不是一次性任务。

在仓库根目录创建：

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

Codex 会从全局和项目目录读取指导。更靠近当前工作目录的 `AGENTS.md` 或 `AGENTS.override.md` 会覆盖更上层的规则。

适合放入 `AGENTS.md` 的内容：

- 构建、测试、lint 命令
- 代码风格和命名规范
- PR 审查重点
- 安全边界
- 文档同步规则
- 子目录专属约定

不适合放入 `AGENTS.md` 的内容：

- 临时需求
- 密钥、token、账号密码
- 冗长产品文档
- 会频繁变化的 issue 详情

## 7. 沙箱与审批

Codex 的安全模型由两部分组成：

- sandbox：技术边界，决定 agent 可以读写哪些文件、是否能访问网络、命令在哪里运行。
- approval policy：审批策略，决定何时暂停并请求人工确认。

常见 sandbox mode：

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| `read-only` | 只能检查文件，不能直接编辑或运行有副作用命令 | 代码审查、需求分析 |
| `workspace-write` | 可在工作区内编辑和运行常规命令 | 日常开发，推荐起点 |
| `danger-full-access` | 取消沙箱限制 | 只在隔离环境中使用 |

常见 approval policy：

| 策略 | 说明 |
|------|------|
| `untrusted` | 对不在信任集合内的命令请求确认 |
| `on-request` | 在沙箱内自主执行，越界时请求确认 |
| `never` | 不弹出审批 |

推荐默认组合：

```bash
codex --sandbox workspace-write --ask-for-approval on-request
```

只有在容器、临时虚拟机、CI sandbox 等外部隔离环境中，才考虑：

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

## 8. `config.toml` 基础

Codex 的本地配置通常在：

```text
~/.codex/config.toml
```

项目级配置可放在可信项目的：

```text
.codex/config.toml
```

示例：

```toml
sandbox_mode = "workspace-write"
approval_policy = "on-request"

[sandbox_workspace_write]
writable_roots = ["."]
```

命令行 `-c key=value` 可以临时覆盖配置：

```bash
codex -c sandbox_mode=\"read-only\" "Review this repository for risky changes"
```

## 9. MCP：连接外部工具

Codex 支持 MCP，用来连接文档、浏览器、Figma、GitHub、Sentry、Linear 或内部系统。

用 CLI 添加 MCP server：

```bash
codex mcp add context7 -- npx -y @upstash/context7-mcp
codex mcp --help
```

在 TUI 中查看：

```text
/mcp
```

也可以写入 `config.toml`：

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
```

MCP 最好和 skills 搭配：

- skill 定义流程
- MCP 提供外部工具和上下文
- `AGENTS.md` 固化项目约定

## 10. Skills 与 Plugins

Codex 的 skills 用于封装可复用工作流。一个 skill 通常是一个目录，里面有 `SKILL.md`，也可以包含脚本、引用资料和资源文件。

最小示例：

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

常见保存位置：

| 范围 | 位置 | 用途 |
|------|------|------|
| 用户级 | `$HOME/.agents/skills` | 个人跨项目工作流 |
| 仓库级 | `.agents/skills` | 团队共享工作流 |
| 插件 | plugin package | 分发多个 skills、MCP 配置和集成 |

Plugins 是可安装分发单元，可以打包多个 skills、MCP server 配置、app 集成和资源。建议先在本地用 skill 打磨流程，稳定后再封装为 plugin。

## 11. Codex 常见开发工作流

### 11.1 探索陌生仓库

```text
Read this repository and summarize:
1. main modules
2. build and test commands
3. likely risk areas
4. where to start for a bug fix
Do not edit files.
```

建议使用 `read-only` sandbox。

### 11.2 实现功能

```text
Add search filtering to the task list.
First inspect the current data flow.
Then propose a plan.
After approval, implement and run tests.
```

### 11.3 修复 bug

```text
The test `task-filter.test.ts` fails.
Reproduce the failure, identify the root cause, make the smallest fix, and run the relevant tests.
```

### 11.4 代码审查

```bash
codex -c sandbox_mode=\"read-only\" "Review the current git diff. Prioritize correctness, security, regressions, and missing tests."
```

### 11.5 文档同步

```text
Update the Chinese and English README indexes after adding a new tutorial.
Then check all relative Markdown links.
```

## 12. 团队使用建议

团队落地 Codex 时，建议按以下顺序建设：

1. 根目录添加 `AGENTS.md`：写清楚测试命令、代码风格、审查重点。
2. 增加必要的 lint、test、typecheck：让 Codex 有可执行反馈。
3. 为重复流程建立 skills：代码审查、发布检查、文档同步、迁移脚本。
4. 用 MCP 接入外部系统：GitHub、Linear、Figma、Sentry、内部文档。
5. 在 GitHub PR 中开启 Codex review：让 Codex 做高信号审查补充。
6. 对高风险仓库配置更严格的 sandbox、approval 和 rules。

## 13. 常见误区

- 把 Codex 当聊天机器人：应该让它读仓库、跑测试、验证结果。
- 不写项目指导：没有 `AGENTS.md` 时，Codex 更容易猜错团队约定。
- 过早全权限：日常开发优先使用 `workspace-write + on-request`。
- 只看生成代码：更重要的是看 diff、测试、失败日志和审查结论。
- 把所有规则塞进 prompt：稳定规则应进入 `AGENTS.md` 或 skill。
- 忽略中英文同步：教程项目尤其要把双语目录和链接作为验收项。

## 14. 与本教程仓库结合

后续本仓库应继续补充：

- 第 17 课：Codex CLI 深入使用
- 第 18 课：`AGENTS.md`、skills、plugins 实战
- 第 19 课：Codex + GitHub Review + Cloud 任务
- 第 20 课：Codex 与 Claude Code 工作流对比和迁移

---

📚 [返回教程目录](../README.md)
