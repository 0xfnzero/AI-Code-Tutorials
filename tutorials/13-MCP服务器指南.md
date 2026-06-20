# 第十三课：MCP 服务器完整指南 - 从入门到精通

> 🎯 **学习目标**：掌握 MCP 服务器的添加、配置和使用，让 Claude Code 功能提升 10 倍

> 🔄 **维护状态（2026-06-20）**：本章原始内容以 2025 年配置方法为基础，后续会按最新 MCP 规范持续复核。版本敏感内容请结合 [第十五课：AI 编程发展跟踪与持续迭代手册](./15-AI编程发展跟踪.md) 和 [更新日志](../CHANGELOG.md) 使用。

## 📚 课程概述

本课程将手把手教你如何正确添加和配置 MCP（Model Context Protocol）服务器，让你的 Claude Code 从简单的 AI 助手变成强大的开发伙伴。

⚡ **快速提示**：如果只想快速添加一个 MCP 服务器，直接跳到["30秒快速上手"](#30秒快速上手)部分。

## 🎯 什么是 MCP？

### MCP 核心概念

MCP（Model Context Protocol）是 Anthropic 推出的开源通信标准，它就像是 AI 助手的"瑞士军刀"。

**MCP 让 Claude Code 可以：**

- 📁 **直接访问和操作本地文件系统**
- 🌐 **连接各种 API 和网络服务**
- 🗄️ **查询和操作数据库**
- 🛠️ **集成各种开发工具**
- 🔧 **自动化日常任务**

### MCP 架构图

```
┌─────────────────┐
│  Claude Code    │
│   (MCP 客户端)   │
└────────┬────────┘
         │
    MCP 协议
         │
┌────────┴────────┐
│  MCP 服务器      │
├─────────────────┤
│ • 文件系统       │
│ • 数据库         │
│ • API 服务       │
│ • 开发工具       │
└─────────────────┘
```

---

## ⚡ 30秒快速上手

如果你赶时间，这是最快的添加方法：

```bash
# 添加文件系统访问（最常用）
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Desktop

# 验证是否成功
claude mcp list
```

**就这么简单！** 但如果遇到错误，请继续阅读详细指南。

---

## 📖 详细添加步骤

### 方法1：命令行添加（推荐新手）

Claude Code 提供了简单的命令行工具来添加 MCP 服务器。

#### 基本语法

```bash
claude mcp add <名称> <命令> [参数...]
```

#### 实际例子

**1. 添加本地文件系统访问**

```bash
claude mcp add my-filesystem -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**2. 带环境变量的例子**

```bash
claude mcp add api-server -e API_KEY=your-key-here -- /path/to/server
```

**3. 指定作用域**

```bash
# 用户级（全局）
claude mcp add github -s user -- npx -y @modelcontextprotocol/server-github

# 项目级（团队共享）
claude mcp add project-tools -s project -- npx -y @your-team/mcp-tools
```

#### 命令行参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| `-s, --scope` | 作用域（local/user/project） | `-s user` |
| `-e, --env` | 环境变量 | `-e API_KEY=xxx` |
| `--` | 分隔符，后面跟命令 | `-- npx -y ...` |

---

### 方法2：直接编辑配置文件（推荐高级用户）

很多开发者觉得 CLI 向导太繁琐，特别是输错了要重来。直接编辑配置文件更高效。

#### 步骤1：找到配置文件位置

**配置文件路径：**
- **macOS/Linux**: `~/.claude.json`
- **Windows**: `%USERPROFILE%\.claude.json`

#### 步骤2：编辑配置文件

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/username/Documents"],
      "env": {}
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "your-github-token"
      }
    },
    "database": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost/mydb"
      }
    }
  }
}
```

#### 步骤3：重启 Claude Code

```bash
# 重启使配置生效
# 或直接关闭并重新打开 Claude Code
```

**配置文件结构说明：**

```json
{
  "mcpServers": {
    "<服务器名称>": {
      "type": "stdio",           // 通信类型（通常是stdio）
      "command": "<命令>",        // 执行命令（如 npx、node）
      "args": ["<参数1>", "<参数2>"], // 命令参数
      "env": {                    // 环境变量
        "KEY": "value"
      }
    }
  }
}
```

---

### 方法3：项目级配置（推荐团队协作）

如果你想让团队成员都能使用相同的 MCP 配置。

#### 添加项目级 MCP 服务器

```bash
# 添加项目级MCP服务器
claude mcp add shared-tools -s project -- npx -y @your-team/mcp-tools
```

这会在项目根目录创建 `.mcp.json` 文件：

```json
{
  "mcpServers": {
    "shared-tools": {
      "command": "npx",
      "args": ["-y", "@your-team/mcp-tools"],
      "env": {}
    }
  }
}
```

#### 团队协作最佳实践

**1. 提交 `.mcp.json` 到 git**

```bash
git add .mcp.json
git commit -m "Add team MCP configuration"
git push
```

**2. 在 README 中说明**

```markdown
## MCP 服务器配置

本项目使用以下 MCP 服务器：
- shared-tools: 团队共享的开发工具
- project-db: 项目数据库访问

安装：项目配置会自动加载，无需手动配置。
```

**3. 敏感信息处理**

```json
{
  "mcpServers": {
    "api-service": {
      "command": "npx",
      "args": ["-y", "@company/api-server"],
      "env": {
        "API_KEY": "${API_KEY}"  // 使用环境变量
      }
    }
  }
}
```

然后在 `.env.local` 中（不要提交到 git）：

```bash
API_KEY=your-actual-api-key
```

---

## 🎚️ MCP 服务器作用域详解

理解作用域对于避免"找不到服务器"的错误至关重要。

### 1. Local 作用域（默认）

**特点：**
- 只在当前目录可用
- 配置存储在 `~/.claude.json` 的 `projects` 部分
- **适合**：个人项目特定工具

**配置示例：**

```json
{
  "projects": {
    "/path/to/my-project": {
      "mcpServers": {
        "local-tool": {
          "command": "npx",
          "args": ["-y", "@my/local-tool"]
        }
      }
    }
  }
}
```

**使用场景：**
```
你：[在项目 A 目录]
    使用 local-tool 处理文件  ✅ 可用

你：[在项目 B 目录]
    使用 local-tool 处理文件  ❌ 不可用
```

---

### 2. User 作用域（全局）

**特点：**
- 在所有项目中都可用
- 使用 `-s user` 标志添加
- **适合**：常用工具如文件系统、数据库客户端

**添加命令：**

```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**配置示例：**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "~/Documents"]
    }
  }
}
```

**使用场景：**
```
你：[在任何项目]
    读取 ~/Documents/file.txt  ✅ 都可用
```

---

### 3. Project 作用域（团队共享）

**特点：**
- 通过 `.mcp.json` 文件共享
- 使用 `-s project` 标志添加
- **适合**：团队共享的项目特定工具

**添加命令：**

```bash
claude mcp add team-tools -s project -- npx -y @team/tools
```

**配置文件：** `.mcp.json`

```json
{
  "mcpServers": {
    "team-tools": {
      "command": "npx",
      "args": ["-y", "@team/tools"]
    }
  }
}
```

**使用场景：**
```
团队成员 A：git clone 项目  ✅ 自动可用
团队成员 B：git clone 项目  ✅ 自动可用
团队成员 C：git clone 项目  ✅ 自动可用
```

---

### 作用域选择指南

| 作用域 | 配置文件 | 适用场景 | 团队共享 |
|--------|---------|---------|---------|
| Local | `~/.claude.json` (projects) | 个人临时工具 | ❌ |
| User | `~/.claude.json` | 个人常用工具 | ❌ |
| Project | `.mcp.json` | 项目团队工具 | ✅ |

**决策树：**

```
需要团队共享？
├─ 是 → Project 作用域
└─ 否 → 在所有项目用？
         ├─ 是 → User 作用域
         └─ 否 → Local 作用域
```

---

## 🔧 10个最实用的 MCP 服务器推荐

基于社区反馈和实际使用经验，这是最值得安装的 MCP 服务器列表。

### 1. 文件系统访问 ⭐⭐⭐⭐⭐

**用途**：让 Claude 直接读写文件，修改代码

```bash
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Projects ~/Desktop
```

**功能：**
- 读取文件内容
- 写入/修改文件
- 创建/删除目录
- 搜索文件

**使用示例：**
```
你：读取 ~/Projects/myapp/src/index.js 并添加错误处理

Claude：[使用 filesystem MCP]
已读取文件，正在添加 try-catch 错误处理...
```

---

### 2. GitHub 集成 ⭐⭐⭐⭐⭐

**用途**：管理 issues、PRs、代码审查

```bash
# 需要 GitHub Personal Access Token
claude mcp add github -s user -e GITHUB_TOKEN=ghp_your_token_here -- npx -y @modelcontextprotocol/server-github
```

**功能：**
- 创建和管理 issues
- 创建和审查 pull requests
- 查看仓库信息
- 管理 GitHub Actions

**使用示例：**
```
你：创建一个 issue 报告登录页面的 bug

Claude：[使用 github MCP]
已创建 issue #123: "Login page bug"
标签：bug, ui
分配给：@you
```

---

### 3. 网页浏览器控制 ⭐⭐⭐⭐

**用途**：自动化网页操作、爬虫、测试

```bash
claude mcp add puppeteer -s user -- npx -y @modelcontextprotocol/server-puppeteer
```

**功能：**
- 自动化浏览器操作
- 网页截图
- 数据爬取
- E2E 测试

**使用示例：**
```
你：访问 example.com 并截图

Claude：[使用 puppeteer MCP]
已访问页面并生成截图：screenshot.png
```

---

### 4. 数据库连接（PostgreSQL）⭐⭐⭐⭐

**用途**：直接查询和操作数据库

```bash
claude mcp add postgres -s user -e DATABASE_URL=postgresql://user:pass@localhost/mydb -- npx -y @modelcontextprotocol/server-postgres
```

**功能：**
- 执行 SQL 查询
- 查看表结构
- 导出数据
- 数据分析

**使用示例：**
```
你：查询 users 表中最近注册的 10 个用户

Claude：[使用 postgres MCP]
SELECT * FROM users ORDER BY created_at DESC LIMIT 10;

结果：
1. john@example.com (2025-01-15)
2. jane@example.com (2025-01-14)
...
```

---

### 5. Fetch 工具（API 调用）⭐⭐⭐⭐

**用途**：调用各种 REST API

```bash
claude mcp add fetch -s user -- npx -y @kazuph/mcp-fetch
```

**功能：**
- HTTP 请求（GET、POST、PUT、DELETE）
- 处理 JSON/XML 响应
- 设置请求头和认证
- 错误处理

**使用示例：**
```
你：调用天气 API 获取北京的天气

Claude：[使用 fetch MCP]
GET https://api.weather.com/v1/weather?city=Beijing

响应：晴天，温度 15°C，湿度 45%
```

---

### 6. 搜索引擎 ⭐⭐⭐⭐

**用途**：搜索最新信息

```bash
# 需要 Brave Search API Key
claude mcp add search -s user -e BRAVE_API_KEY=your_key -- npx -y @modelcontextprotocol/server-brave-search
```

**功能：**
- 网页搜索
- 新闻搜索
- 图片搜索
- 实时信息

**使用示例：**
```
你：搜索"React 19 新特性"

Claude：[使用 search MCP]
找到以下结果：
1. React 19 正式发布 - 官方博客
2. React 19 升级指南 - Medium
3. React 19 性能对比 - Dev.to
```

---

### 7. Slack 集成 ⭐⭐⭐

**用途**：发送消息、管理频道

```bash
claude mcp add slack -s user -e SLACK_TOKEN=xoxb-your-token -- npx -y @modelcontextprotocol/server-slack
```

**功能：**
- 发送消息到频道
- 创建/管理频道
- 上传文件
- 获取消息历史

**使用示例：**
```
你：给 #dev-team 频道发送部署完成的消息

Claude：[使用 slack MCP]
已发送到 #dev-team：
"✅ Production deployment completed successfully!"
```

---

### 8. 时间管理 ⭐⭐⭐

**用途**：时区转换、日期计算

```bash
claude mcp add time -s user -- npx -y @modelcontextprotocol/server-time
```

**功能：**
- 时区转换
- 日期格式化
- 时间计算
- 工作日计算

**使用示例：**
```
你：明天北京时间下午 3 点是纽约几点？

Claude：[使用 time MCP]
北京时间：2025-01-16 15:00 CST
纽约时间：2025-01-16 02:00 EST
```

---

### 9. 内存存储 ⭐⭐⭐⭐

**用途**：跨对话保存信息

```bash
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory
```

**功能：**
- 存储键值对
- 跨会话保存数据
- 临时缓存
- 配置管理

**使用示例：**
```
你：记住我的 API key 是 abc123

Claude：[使用 memory MCP]
已保存：API_KEY = abc123

[下次对话]
你：我的 API key 是什么？

Claude：[从 memory MCP 读取]
您的 API key 是 abc123
```

---

### 10. Sequential Thinking（思维链）⭐⭐⭐⭐⭐

**用途**：复杂问题分步骤思考

```bash
claude mcp add thinking -s user -- npx -y @modelcontextprotocol/server-sequential-thinking
```

**功能：**
- 分步思考复杂问题
- 显示推理过程
- 提高回答质量
- 调试逻辑

**使用示例：**
```
你：设计一个高可用的微服务架构

Claude：[使用 thinking MCP]
让我分步思考这个问题...

步骤 1：识别核心需求
- 高可用性（99.9%+ uptime）
- 可扩展性
- 容错能力

步骤 2：选择架构模式
- 微服务 vs 单体
- 同步 vs 异步通信

步骤 3：设计方案
[详细设计...]
```

---

## 🐛 常见错误及解决方案

### 错误1：工具名称验证失败

**错误信息：**
```
API Error 400: "tools.11.custom.name: String should match pattern '^[a-zA-Z0-9_-]{1,64}'"
```

**原因：**
- 服务器名称包含非法字符
- 名称太长（超过64字符）
- 使用了空格或特殊字符

**解决方案：**

```bash
# ❌ 错误示例
claude mcp add "my server" -- ...        # 包含空格
claude mcp add my@server -- ...          # 包含@符号
claude mcp add very-long-server-name-that-exceeds-the-maximum-allowed-length -- ...  # 太长

# ✅ 正确示例
claude mcp add my-server -- ...          # 使用连字符
claude mcp add my_server -- ...          # 使用下划线
claude mcp add myserver -- ...           # 纯字母
```

**命名规范：**
- 只使用字母（a-z, A-Z）
- 数字（0-9）
- 连字符（-）
- 下划线（_）
- 长度不超过64个字符

---

### 错误2：找不到 MCP 服务器

**错误信息：**
```
MCP server 'my-server' not found
```

**原因分析及解决：**

**1. 检查作用域设置**

```bash
# 查看所有MCP服务器
claude mcp list

# 确认服务器在正确的作用域
# Local: 只在特定目录可用
# User: 全局可用
# Project: 项目级可用
```

**2. 确认在正确的目录**

```bash
# 对于 Local 作用域的服务器
pwd  # 检查当前目录
cd /path/to/your/project  # 切换到项目目录
claude mcp list  # 再次检查
```

**3. 重启 Claude Code**

```bash
# 退出并重新启动
# 或使用命令
claude --restart
```

**4. 手动验证配置文件**

```bash
# macOS/Linux
cat ~/.claude.json

# Windows
type %USERPROFILE%\.claude.json

# 检查项目配置
cat .mcp.json
```

---

### 错误3：协议版本错误

**错误信息：**
```json
{
  "error": {
    "protocolVersion": "Required"
  }
}
```

**原因：**
这是 Claude Code 的已知 bug，MCP 服务器未返回正确的协议版本。

**解决方案：**

**方案1：使用包装脚本**

创建 `mcp-wrapper.sh`：

```bash
#!/bin/bash
# mcp-wrapper.sh

# 运行实际的MCP服务器，并注入协议版本
npx -y @modelcontextprotocol/server-filesystem "$@" | \
  jq 'if .jsonrpc then . + {protocolVersion: "2024-11-05"} else . end'
```

使其可执行：

```bash
chmod +x mcp-wrapper.sh
```

使用包装脚本：

```bash
claude mcp add filesystem -- ./mcp-wrapper.sh ~/Documents
```

**方案2：更新 Claude Code**

```bash
# 更新到最新版本
npm update -g @anthropic-ai/claude-code

# 或使用 npx 强制使用最新版本
npx @anthropic-ai/claude-code@latest
```

**方案3：等待修复**

这个问题已经在 GitHub 上报告，关注：
- https://github.com/anthropics/claude-code/issues/xxx

---

### 错误4：Windows 路径问题

**错误信息：**
```
Error: Cannot find module 'C:UsersusernameDocuments'
```

**原因：**
Windows 路径使用反斜杠（\），在配置中需要转义或使用正斜杠。

**解决方案：**

```bash
# ❌ 错误：使用反斜杠
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem C:\Users\username\Documents

# ✅ 方法1：使用正斜杠
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem C:/Users/username/Documents

# ✅ 方法2：使用双反斜杠
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem C:\\Users\\username\\Documents

# ✅ 方法3：使用环境变量
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem %USERPROFILE%/Documents
```

**在配置文件中：**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "C:/Users/username/Documents"  // 使用正斜杠
      ]
    }
  }
}
```

---

### 错误5：权限问题

**错误信息：**
```
Error: EACCES: permission denied
```

**解决方案：**

**macOS/Linux：**

```bash
# 方法1：使用用户目录（推荐）
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents

# 方法2：修改文件权限
chmod 755 /path/to/directory

# 方法3：使用sudo（不推荐）
sudo claude mcp add ...
```

**Windows：**

```powershell
# 方法1：以管理员身份运行PowerShell/CMD
# 右键点击 PowerShell/CMD -> "以管理员身份运行"

# 方法2：修改文件夹权限
# 右键文件夹 -> 属性 -> 安全 -> 编辑 -> 添加完全控制权限
```

**最佳实践：**
- 将 MCP 服务器安装在用户目录
- 避免需要 root/管理员权限的操作
- 使用环境变量管理敏感路径

---

## 🔍 调试技巧

当遇到问题时，这些调试方法可以帮你快速定位。

### 1. 启用调试模式

```bash
# 启动时添加 --mcp-debug 标志
claude --mcp-debug

# 会显示详细的MCP通信日志
```

**输出示例：**
```
[MCP Debug] Connecting to server: filesystem
[MCP Debug] Request: {"jsonrpc":"2.0","id":1,"method":"tools/list"}
[MCP Debug] Response: {"jsonrpc":"2.0","id":1,"result":{"tools":[...]}}
```

---

### 2. 查看 MCP 状态

```bash
# 在 Claude Code 中输入
/mcp

# 显示所有MCP服务器状态
```

**输出示例：**
```
MCP Servers Status:
✅ filesystem (user) - Connected
✅ github (user) - Connected
❌ database (local) - Disconnected
⚠️ api-service (project) - Not configured
```

---

### 3. 查看日志文件

**macOS/Linux：**

```bash
# 实时查看日志
tail -f ~/Library/Logs/Claude/mcp*.log

# 查看最近的错误
grep "ERROR" ~/Library/Logs/Claude/mcp*.log | tail -20

# 查看特定服务器的日志
grep "filesystem" ~/Library/Logs/Claude/mcp*.log
```

**Windows：**

```powershell
# 查看日志
type "%APPDATA%\Claude\logs\mcp*.log"

# 查看最近的日志
Get-Content "$env:APPDATA\Claude\logs\mcp*.log" -Tail 50

# 搜索错误
Select-String -Path "$env:APPDATA\Claude\logs\mcp*.log" -Pattern "ERROR"
```

---

### 4. 手动测试服务器

```bash
# 直接运行服务器命令，看是否有输出
npx -y @modelcontextprotocol/server-filesystem ~/Documents

# 应该看到JSON-RPC格式的输出
```

**正常输出示例：**
```json
{
  "jsonrpc": "2.0",
  "method": "server/ready",
  "params": {
    "protocolVersion": "2024-11-05"
  }
}
```

---

### 5. 验证配置文件语法

```bash
# 使用jq验证JSON语法
cat ~/.claude.json | jq .

# 如果有语法错误，jq会报错
```

**常见语法错误：**
```json
{
  "mcpServers": {
    "test": {
      "command": "npx",
      "args": ["test"],  // ❌ 末尾多了逗号
    }
  }
}
```

**正确示例：**
```json
{
  "mcpServers": {
    "test": {
      "command": "npx",
      "args": ["test"]  // ✅ 没有逗号
    }
  }
}
```

---

### 6. 使用详细模式运行

```bash
# 启用详细输出
claude --verbose

# 结合MCP调试
claude --verbose --mcp-debug
```

---

## 🇨🇳 中文用户特别注意事项

### 1. 中文路径问题

**问题：** 路径中包含中文字符可能导致错误。

**避免使用中文路径：**

```bash
# ❌ 避免
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/文档

# ✅ 推荐：使用英文路径
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents

# ✅ 或使用完整路径
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem /Users/username/Documents
```

**如果必须使用中文路径：**

```bash
# 使用引号包裹
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem "~/文档"

# 或使用URL编码
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/\%E6\%96\%87\%E6\%A1\%A3
```

---

### 2. 代理配置

**问题：** 在中国大陆访问 npm 可能较慢或失败。

**配置代理：**

```bash
# HTTP代理
npm config set proxy http://your-proxy:port
npm config set https-proxy http://your-proxy:port

# SOCKS代理
npm config set proxy socks5://127.0.0.1:1080
npm config set https-proxy socks5://127.0.0.1:1080

# 然后添加MCP服务器
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**取消代理：**

```bash
npm config delete proxy
npm config delete https-proxy
```

---

### 3. 国内镜像源

**使用淘宝 npm 镜像加速下载：**

**临时使用：**

```bash
claude mcp add fs -- npx -y --registry=https://registry.npmmirror.com @modelcontextprotocol/server-filesystem ~/Documents
```

**永久设置：**

```bash
# 设置淘宝镜像
npm config set registry https://registry.npmmirror.com

# 验证设置
npm config get registry

# 然后正常添加MCP服务器
claude mcp add fs -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**恢复官方源：**

```bash
npm config set registry https://registry.npmjs.org
```

---

### 4. 字符编码问题

**问题：** Windows 中文环境可能出现乱码。

**解决方案：**

```powershell
# Windows PowerShell
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# Windows CMD
chcp 65001
```

**在配置文件中使用UTF-8：**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "D:/Projects"],
      "env": {
        "LANG": "zh_CN.UTF-8"
      }
    }
  }
}
```

---

## 💡 最佳实践建议

### 1. 按需添加

**不要一次性添加太多 MCP 服务器**，会影响性能。

```bash
# ❌ 不推荐：一次添加所有
claude mcp add server1 -- ...
claude mcp add server2 -- ...
claude mcp add server3 -- ...
# ... 添加了20个服务器

# ✅ 推荐：只添加常用的
claude mcp add filesystem -s user -- ...
claude mcp add github -s user -- ...
# 够用就好
```

**建议：**
- 新手：2-3个常用服务器
- 进阶：5-7个常用服务器
- 高级：根据需要动态添加

---

### 2. 定期清理

**删除不用的服务器：**

```bash
# 查看所有服务器
claude mcp list

# 删除不用的服务器
claude mcp remove old-server

# 批量清理
for server in old1 old2 old3; do
  claude mcp remove $server
done
```

**清理策略：**
- 每月审查一次已安装的服务器
- 删除3个月未使用的服务器
- 保留核心服务器（如 filesystem）

---

### 3. 安全第一

**只添加可信的 MCP 服务器**，特别是需要网络访问的。

**安全检查清单：**

- ✅ 官方发布的服务器（@modelcontextprotocol/*）
- ✅ 知名公司/组织发布的服务器
- ✅ GitHub star 数多、活跃维护的项目
- ⚠️ 需要敏感权限（文件系统、网络）的服务器
- ❌ 来源不明的服务器

**审查代码：**

```bash
# 查看服务器源代码
npm view @modelcontextprotocol/server-filesystem repository
# 访问GitHub查看代码

# 检查依赖
npm view @modelcontextprotocol/server-filesystem dependencies
```

---

### 4. 备份配置

**定期备份 `~/.claude.json` 文件：**

```bash
# macOS/Linux
cp ~/.claude.json ~/.claude.json.backup

# 或使用日期备份
cp ~/.claude.json ~/.claude.json.$(date +%Y%m%d)

# Windows
copy %USERPROFILE%\.claude.json %USERPROFILE%\.claude.json.backup
```

**使用版本控制：**

```bash
# 创建配置仓库
mkdir ~/claude-config
cd ~/claude-config
git init

# 链接配置文件
ln -s ~/.claude.json claude.json

# 提交更改
git add claude.json
git commit -m "Update MCP configuration"
```

---

### 5. 团队协作

**使用 project 作用域共享常用配置。**

**团队配置示例：**

`.mcp.json`：

```json
{
  "mcpServers": {
    "team-db": {
      "command": "npx",
      "args": ["-y", "@company/db-mcp"],
      "env": {
        "DB_HOST": "${DB_HOST}",
        "DB_USER": "${DB_USER}"
      }
    },
    "team-api": {
      "command": "npx",
      "args": ["-y", "@company/api-mcp"]
    }
  }
}
```

`.env.example`：

```bash
# 团队成员复制为 .env.local 并填写
DB_HOST=localhost
DB_USER=your_username
DB_PASSWORD=your_password
```

`README.md`：

```markdown
## MCP 配置

1. 复制 `.env.example` 为 `.env.local`
2. 填写你的数据库凭证
3. 运行 `claude` 即可使用团队 MCP 服务器
```

---

## 🚀 进阶技巧

### 1. 创建自定义 MCP 服务器

如果现有的 MCP 服务器不能满足需求，可以创建自己的。

**基础服务器示例：**

`my-mcp-server.js`：

```javascript
import { Server } from '@modelcontextprotocol/sdk';

// 创建服务器
const server = new Server({
  name: 'my-custom-server',
  version: '1.0.0',
});

// 定义工具列表
server.setRequestHandler('tools/list', async () => {
  return {
    tools: [{
      name: 'my_custom_tool',
      description: '自定义工具：处理文本',
      inputSchema: {
        type: 'object',
        properties: {
          text: {
            type: 'string',
            description: '要处理的文本'
          },
          operation: {
            type: 'string',
            enum: ['uppercase', 'lowercase', 'reverse'],
            description: '操作类型'
          }
        },
        required: ['text', 'operation']
      }
    }]
  };
});

// 实现工具逻辑
server.setRequestHandler('tools/call', async (request) => {
  const { name, arguments: args } = request.params;

  if (name === 'my_custom_tool') {
    const { text, operation } = args;

    let result;
    switch (operation) {
      case 'uppercase':
        result = text.toUpperCase();
        break;
      case 'lowercase':
        result = text.toLowerCase();
        break;
      case 'reverse':
        result = text.split('').reverse().join('');
        break;
      default:
        throw new Error(`Unknown operation: ${operation}`);
    }

    return {
      content: [{
        type: 'text',
        text: result
      }]
    };
  }

  throw new Error(`Unknown tool: ${name}`);
});

// 启动服务器
server.start();
```

**添加自定义服务器：**

```bash
# 安装依赖
npm install @modelcontextprotocol/sdk

# 添加到Claude Code
claude mcp add my-server -- node /path/to/my-mcp-server.js
```

**使用自定义服务器：**

```
你：使用 my_custom_tool 将 "Hello World" 转为大写

Claude：[使用 my-server MCP]
HELLO WORLD
```

---

### 2. 批量配置脚本

创建一个脚本一次性配置所有常用 MCP 服务器。

`setup-mcp.sh`：

```bash
#!/bin/bash

echo "🚀 开始配置 Claude Code MCP 服务器..."
echo ""

# 1. 文件系统
echo "📁 添加文件系统访问..."
claude mcp add filesystem -s user -- npx -y @modelcontextprotocol/server-filesystem ~/Documents ~/Projects
echo "✅ 文件系统配置完成"
echo ""

# 2. GitHub
echo "🐙 配置 GitHub 集成..."
read -p "请输入 GitHub Personal Access Token: " github_token
claude mcp add github -s user -e GITHUB_TOKEN=$github_token -- npx -y @modelcontextprotocol/server-github
echo "✅ GitHub 配置完成"
echo ""

# 3. Puppeteer
echo "🌐 添加浏览器控制..."
claude mcp add puppeteer -s user -- npx -y @modelcontextprotocol/server-puppeteer
echo "✅ Puppeteer 配置完成"
echo ""

# 4. 数据库（可选）
read -p "是否配置数据库访问？(y/n) " configure_db
if [ "$configure_db" = "y" ]; then
  read -p "请输入数据库URL: " db_url
  claude mcp add postgres -s user -e DATABASE_URL=$db_url -- npx -y @modelcontextprotocol/server-postgres
  echo "✅ 数据库配置完成"
fi
echo ""

# 5. 其他服务器
echo "🔧 添加其他常用工具..."
claude mcp add memory -s user -- npx -y @modelcontextprotocol/server-memory
claude mcp add time -s user -- npx -y @modelcontextprotocol/server-time
echo "✅ 其他工具配置完成"
echo ""

# 完成
echo "🎉 所有 MCP 服务器配置完成！"
echo ""
echo "查看已安装的服务器："
claude mcp list
```

**使用脚本：**

```bash
# 添加执行权限
chmod +x setup-mcp.sh

# 运行脚本
./setup-mcp.sh
```

---

### 3. 环境特定配置

为不同环境（开发、测试、生产）配置不同的 MCP 服务器。

**目录结构：**

```
~/claude-configs/
├── development.json
├── testing.json
└── production.json
```

**development.json：**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "~/dev"]
    },
    "database": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://localhost/dev_db"
      }
    }
  }
}
```

**切换脚本：**

```bash
#!/bin/bash
# switch-env.sh

ENV=$1

if [ -z "$ENV" ]; then
  echo "Usage: ./switch-env.sh [development|testing|production]"
  exit 1
fi

cp ~/claude-configs/$ENV.json ~/.claude.json
echo "Switched to $ENV environment"
claude mcp list
```

**使用：**

```bash
./switch-env.sh development  # 切换到开发环境
./switch-env.sh production   # 切换到生产环境
```

---

## 📚 总结

通过本课程，你应该已经掌握了：

### ✅ 核心技能

1. **理解 MCP 概念**
   - MCP 是什么
   - MCP 如何工作
   - MCP 的作用域

2. **添加 MCP 服务器**
   - 命令行添加
   - 配置文件编辑
   - 项目级配置

3. **使用实用服务器**
   - 10个最常用的 MCP 服务器
   - 每个服务器的用途和示例

4. **解决常见问题**
   - 5大类常见错误
   - 调试技巧
   - 中文环境特殊处理

5. **最佳实践**
   - 安全配置
   - 性能优化
   - 团队协作

### 🎯 下一步

- 📖 [第九课：提示词优化技巧](./09-提示词优化技巧.md) - 提升交互效率
- 📖 [第十课：AI 子代理系统](./10-AI子代理系统.md) - 构建专家团队
- 📖 [第十二课：最佳实践](./12-最佳实践.md) - Anthropic 官方推荐

### 💡 实践建议

1. **从基础开始**
   - 先添加 filesystem 服务器
   - 熟悉基本操作
   - 逐步添加其他服务器

2. **解决实际问题**
   - 用 MCP 服务器解决日常任务
   - 记录使用经验
   - 优化配置

3. **持续学习**
   - 关注 MCP 社区
   - 尝试新的服务器
   - 分享使用经验

---

正确配置 MCP 服务器后，你的 Claude Code 将从一个简单的 AI 助手变成强大的开发伙伴，开发效率将会大幅提升！

📚 [返回教程目录](../README.md)
