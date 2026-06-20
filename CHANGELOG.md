# 更新日志 / Changelog

本文件记录教程内容的持续更新，重点说明日期、依据来源和影响范围。

## 2026-06-20

### Added

- 新增中文第 15 课：[AI 编程发展跟踪与持续迭代手册](./tutorials/15-AI编程发展跟踪.md)。
- Added English Lesson 15: [AI Coding Update Tracker and Iteration Playbook](./tutorials/en/15-ai-coding-update-tracker.md).
- 建立月度更新检查清单，覆盖 Claude Code、MCP、Skills、Plugins、Agent SDK 和生产安全。

### Changed

- 更新中文与英文 README，将第 15 课加入课程大纲和持续更新入口。
- 修正英文 README 中课程数量已过期的问题。
- 给第 13 课 MCP 指南和第 14 课完整使用指南增加维护状态说明，避免继续把 2025 年内容表述为“最新”。

### Verification Baseline

- Claude Code latest npm version checked with `npm view @anthropic-ai/claude-code version dist-tags time.modified --json`: `2.1.183`, modified `2026-06-19T01:20:18.438Z`.
- Official Claude Code changelog checked: [Claude Code Changelog](https://code.claude.com/docs/en/changelog).
- MCP documentation checked: [MCP introduction](https://modelcontextprotocol.io/docs/getting-started/intro) and [MCP 2025-11-25 changelog](https://modelcontextprotocol.io/specification/2025-11-25/changelog).
- Claude Code Skills checked: [Claude Code Skills](https://code.claude.com/docs/en/skills).
- Claude Agent SDK checked: [Agent SDK overview](https://code.claude.com/docs/en/agent-sdk/overview).

### Next

- Add a dedicated Skills lesson.
- Refresh the MCP guide for the latest stable specification.
- Update deployment lessons beyond Heroku-only examples.
- Re-audit CLI command examples in Lesson 14 against current `claude --help`.
