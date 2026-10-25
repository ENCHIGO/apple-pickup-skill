# Apple Pickup Skill

让 AI agent 查询 Apple Store 到店取货库存、监控指定门店到货的 skill。适用于 WorkBuddy、Claude Code、Codex 以及其他支持 `SKILL.md` 的 agent。

背后的程序是 [Apple Pickup Watcher](https://github.com/ENCHIGO/apple-pickup-watcher) 的命令行版 `apw`。skill 告诉 agent 怎么用它；如果电脑上还没有 `apw`，skill 自带的安装脚本会从官方 Release 下载预编译包，校验 sha256 后安装。

- 支持 25 个地区的 Apple Store，以及 iPhone、iPad、Mac、Apple Watch
- 只查库存，不会加入购物袋，也不会下单
- 查询失败、被拦截时报告「未知」和原因，不会当成无货

## 能做什么

对 agent 说，例如：

- 「上海哪几家 Apple Store 现在能取 iPhone 18 Pro 256GB？」
- 「帮我盯着南京东路店的 iPhone Duo 512GB 夜空色，有货告诉我，最多盯 30 分钟。」
- 「香港有哪些 Apple Store？」

agent 会先在内置目录里找到门店编号和型号零件号，再查询 Apple 官网的取货接口。

## 安装

skill 是 `skills/apple-pickup-watcher/` 这一个目录（`SKILL.md`、`scripts/`、`agents/`），复制或导入到 agent 的技能目录即可。

### WorkBuddy

任选一种：

- 在对话里把本仓库地址 `https://github.com/ENCHIGO/apple-pickup-skill` 发给 WorkBuddy，让它安装这个 skill。
- 下载 [Releases](https://github.com/ENCHIGO/apple-pickup-skill/releases/latest) 里的 `apple-pickup-watcher.zip`，在技能面板导入。
- 手动把 `skills/apple-pickup-watcher/` 复制到 `~/.workbuddy/skills/apple-pickup-watcher/`，重启 WorkBuddy。

### Claude Code / Codex / 其他 agent

用 [Skills CLI](https://github.com/vercel-labs/skills)（需要 Node.js 22.20+）：

```bash
npx skills add ENCHIGO/apple-pickup-skill --skill apple-pickup-watcher --agent claude-code --global
npx skills add ENCHIGO/apple-pickup-skill --skill apple-pickup-watcher --agent codex --global
```

或手动复制目录：

| Agent | 目录 |
|---|---|
| Claude Code | `~/.claude/skills/apple-pickup-watcher/` |
| Codex | `${CODEX_HOME:-~/.codex}/skills/apple-pickup-watcher/` |
| WorkBuddy | `~/.workbuddy/skills/apple-pickup-watcher/` |

agent 需要能执行命令行（Shell）。

### 安装 apw 程序

第一次使用时，agent 会在告诉你之后运行 skill 里的安装脚本。也可以自己装：

```bash
# macOS（Apple Silicon / Intel）、x86_64 Linux，装到 ~/.local/bin/apw
sh skills/apple-pickup-watcher/scripts/install-apw.sh
```

```powershell
# Windows x64，装到 %LOCALAPPDATA%\apw\bin\apw.exe
powershell -ExecutionPolicy Bypass -File skills\apple-pickup-watcher\scripts\install-apw.ps1
```

`APW_VERSION=v0.7.0` 可以指定版本，`APW_INSTALL_DIR` 可以指定安装目录。脚本只从 `github.com/ENCHIGO/apple-pickup-watcher/releases` 下载，sha256 不匹配就放弃安装。访问不了 GitHub 的网络需要代理，或者从 [Release 页面](https://github.com/ENCHIGO/apple-pickup-watcher/releases/latest) 手动下载 `apw-cli-*` 包，解压后把 `apw` 放进 PATH。

## 注意

- Apple 按出口 IP 限制取货查询频率，连续约 30 次就会被拦（HTTP 541）十几分钟。skill 会让 agent 用一个 `apw watch` 进程持续监控，而不是反复查询。
- agent 关掉或命令超时，监控就停了。需要长时间盯货、要系统通知，用[桌面版](https://github.com/ENCHIGO/apple-pickup-watcher/releases/latest)。
- CLI 的完整说明见 [docs/cli.md](https://github.com/ENCHIGO/apple-pickup-watcher/blob/main/docs/cli.md)。

## 许可

GPL-3.0-or-later，与 [Apple Pickup Watcher](https://github.com/ENCHIGO/apple-pickup-watcher) 相同。
