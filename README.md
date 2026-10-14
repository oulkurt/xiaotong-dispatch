# 小瞳派单（xiaotong-dispatch）

让 Codex、Claude Code、Trae、WorkBuddy、千问办公（QwenWork）和豆包办公（豆包工作）把任务交给你电脑上的[小瞳 Work](https://github.com/oulkurt/xiaotong-work)。

> **状态：开发中。** 需要小瞳 Work 0.1.17 或更高版本（尚未发布）。下面的接入方式都还在等实机测试。

## 它做什么

对你正在用的 agent 说「让小瞳帮我…」或「小瞳，帮我…」。agent 会：

1. 问你在小瞳的哪个项目里办。你在话里点了项目名，就直接用这个项目。
2. 把任务交给小瞳。小瞳在这个项目里执行，你可以在小瞳 Work 窗口里实时看进度。
3. 等小瞳做完，把结果带回当前对话。约 8 分钟还没做完，就转到后台；之后问一句「小瞳好了吗」就能取回结果。

之后还可以说「让小瞳接着…」「让小瞳停下」「在小瞳里打开」。

## 工作原理

```text
你的 agent ── Skill ──▶ MCP（proma mcp）或 CLI（proma task）
                                  │ 127.0.0.1 + 本机令牌
                                  ▼
                       小瞳 Work 本机接单口 ──▶ 项目里的 Agent 会话
```

- 接单口只监听本机 127.0.0.1。小瞳 Work 每次启动都换新令牌。
- 派来的任务在小瞳里免审批执行，和企业微信、钉钉机器人一样。不需要时，在小瞳 Work 设置里关闭「允许外部 Agent 调度」。
- 小瞳 Work 没有运行时，会被自动启动。

## 安装

### 第一步：安装 Skill

编程类 agent，一条命令装好：

```bash
npx skills add oulkurt/xiaotong-dispatch --agent claude-code codex trae trae-cn codebuddy --global
```

办公类 agent：从 [Releases](https://github.com/oulkurt/xiaotong-dispatch/releases) 下载 `xiaotong-dispatch.zip`（第一个版本发布后提供），然后在界面里上传：

| agent | 上传位置 |
|---|---|
| WorkBuddy | 技能页面，上传本地技能 ZIP |
| 千问办公（QwenWork） | 技能页面上传 ZIP；或解压到 `~/.qwenworkcn/skills/` |
| 豆包办公（豆包工作） | 插件·技能·伙伴 → 技能 → 添加 → 上传 |

### 第二步：接入 MCP（推荐；Codex 必须接）

agent 通过 MCP 连接小瞳。`proma` 随小瞳 Work 一起安装，路径固定：

- macOS / Linux：`~/.xiaotong-work/bin/proma`
- Windows：`%USERPROFILE%\.xiaotong-work\bin\proma.exe`

多数配置文件不展开 `~`，请写成绝对路径，例如 `/Users/你的用户名/.xiaotong-work/bin/proma`。

**Claude Code**

```bash
claude mcp add --scope user xiaotong -- ~/.xiaotong-work/bin/proma mcp
```

**Codex**：写入 `~/.codex/config.toml`

```toml
[mcp_servers.xiaotong]
command = "/Users/你的用户名/.xiaotong-work/bin/proma"
args = ["mcp"]
```

**Trae、WorkBuddy、千问办公**：使用下面这段 JSON。

```json
{
  "mcpServers": {
    "xiaotong": {
      "command": "/Users/你的用户名/.xiaotong-work/bin/proma",
      "args": ["mcp"]
    }
  }
}
```

| agent | 放在哪里 |
|---|---|
| Trae | 设置里的 MCP 配置，或项目的 `.trae/mcp.json` |
| WorkBuddy | `~/.workbuddy/mcp.json` |
| 千问办公 | 扩展 → 连接器 → 添加，粘贴 JSON |

**豆包办公**：插件 → 添加 → 新建自定义插件。类型选 STDIO，命令填 `proma` 的绝对路径，参数填 `mcp`。

## 已知限制

- **云端模式和网页版不可用。** 它们运行在厂商的服务器上，连不到你的电脑。请使用桌面端的本地模式。
- **Codex 默认沙箱禁止命令连接本机。** 请接入 MCP；MCP 服务不受这个限制。
- **千问办公打开「隔离工作环境」后**，能否连接本机还没有验证。

## 许可

[MIT](LICENSE)
