# xiaotong-dispatch 工程约定

本仓库只发布 Skill。派单接口由小瞳 Work 实现：桌面端的本机接单口，以及随安装包分发的 `proma` CLI 与 `proma mcp`。

## 必须遵守

- `skills/xiaotong-dispatch/SKILL.md` 写定的工具名、参数和返回字段就是接口契约。修改契约时，先修改小瞳 Work，再修改本仓库。
- `references/cli.md` 的命令与 MCP 工具一一对应。两者的返回字段必须相同。
- 修改 `skills/xiaotong-dispatch/` 下的任何文件，都把 `SKILL.md` frontmatter 的 `metadata.version` 加一个 patch 版本。
- `SKILL.md` 保持在 500 行以内。细节写进 `references/`。
- `name` 只用 ASCII 小写字母、数字和单个连字符，并与目录名一致。`description` 不超过 1024 字符。
- 文档用中文。一句只说一件事；同一概念全仓库只用一个词：小瞳、小瞳 Work、项目、任务、任务号、任务正文。
