# proma 命令参考（派单部分）

只在没有 `xiaotong_*` MCP 工具时使用本文件。命令与 MCP 工具一一对应，返回字段相同。

## 找到 proma

按顺序使用第一个存在的路径：

1. 环境变量 `XIAOTONG_CLI`。
2. macOS 与 Linux：`~/.xiaotong-work/bin/proma`。
3. Windows：`%USERPROFILE%\.xiaotong-work\bin\proma.exe`。
4. macOS：`/Applications/小瞳 Work.app/Contents/Resources/bin/proma`。

路径都不存在时，请用户安装或升级小瞳 Work，并打开一次。小瞳 Work 启动时会刷新第 2、3 项的路径。

## 约定

- 每条命令都加 `--json`。输出一行 JSON。
- 退出码不为 0：命令本身出错，例如连不上小瞳 Work、参数错误。错误信息在 `error` 字段。
- 任务失败不算命令出错：退出码为 0，`status` 为 `failed`。

## 命令

列出项目，并按当前目录推荐项目：

```bash
proma project list --cwd "$PWD" --json
# {"projects":[{"id":"…","name":"…","rootPath":"…","updatedAt":0}],"suggestedProjectId":"…"}
```

派单。任务正文从标准输入读取，避免引号转义出错：

```bash
proma task run --project <projectId> --caller "Claude Code" --json <<'EOF'
任务正文
EOF
# {"sessionId":"…","projectId":"…","projectName":"…","status":"running"}
```

可选参数：

- `--session <sessionId>`：续做同一个任务。
- `--file <绝对路径>`：交给小瞳的文件，可重复。

Windows PowerShell 用 here-string 传任务正文：

```powershell
@'
任务正文
'@ | proma task run --project <projectId> --caller "Codex" --json
```

等待结果，最多 45 秒：

```bash
proma task wait <sessionId> --timeout 45 --json
# {"status":"running|completed|failed|stopped","reply":"…","truncated":false,"error":null,"elapsedMs":0}
```

- `--timeout 0`：立即返回当前状态。
- `reply` 超过 20000 字时被截断，`truncated` 为 `true`。完整内容在小瞳 Work 里查看。

其他命令：

```bash
proma task list --limit 10 --json      # 最近派出的任务
proma task stop <sessionId> --json     # 停止任务
proma task open <sessionId> --json     # 在小瞳 Work 窗口打开任务
```

## 宿主的命令超时

- Claude Code：Bash 默认超时 120 秒。`--timeout 45` 不会触发超时。
- Codex：命令默认超时很短，调用时把 `timeout_ms` 设为 60000。Codex 默认沙箱禁止命令连接本机，优先使用 MCP 工具。
