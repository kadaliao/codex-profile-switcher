# codex-safe-switch

[![PyPI](https://img.shields.io/pypi/v/codex-safe-switch.svg)](https://pypi.org/project/codex-safe-switch/)
[![CI](https://github.com/kadaliao/codex-safe-switch/actions/workflows/ci.yml/badge.svg)](https://github.com/kadaliao/codex-safe-switch/actions/workflows/ci.yml)

中文 | [English](README.en.md)

在官方 ChatGPT、第三方 relay、多组 API key 之间切换 Codex CLI 配置。

## 30 秒上手

先[安装 `uv`](https://docs.astral.sh/uv/getting-started/installation/)，再安装本工具：

```bash
uv tool install codex-safe-switch
codex-safe-switch
```

第一次运行会从当前 `~/.codex/config.toml` 导入 provider 配置切片。

已经安装过时，这样升级到最新版：

```bash
uv tool upgrade codex-safe-switch
```

之后常用的就三个命令：

```bash
codex-safe-switch ls            # 看已有 profiles
codex-safe-switch use myrelay   # 切到 relay / 自定义 provider
codex-safe-switch official      # 切回官方 ChatGPT
```

## 保存一个新 profile

先把 Codex 手动配置到能用的状态。比如你已经配好了某个 relay，并确认 `codex` 能跑。

然后保存：

```bash
codex-safe-switch save myrelay
```

以后就可以这样切回去：

```bash
codex-safe-switch use myrelay
```

## 第一次用时会怎样

| 你现在的状态 | 会发生什么 |
| --- | --- |
| 正在用官方 ChatGPT | 自动保存成 `official` |
| 正在用 relay / 自定义 provider | 自动保存成当前 provider 名 |
| 登录过 ChatGPT，但当前正在用 relay | 先保存当前 relay；以后跑 `codex-safe-switch official` 回官方 |
| 还没有 `~/.codex/config.toml` | 提示你先配置一次 Codex |

`codex-safe-switch official` 不需要提前存在 `official` profile。缺少时会自动创建默认官方配置再切换。

## 它不会碰登录信息

这个工具只保存和切换 provider 配置切片：`model`、`model_provider`、`model_reasoning_effort`、`model_reasoning_summary`、`model_verbosity`、`wire_api`、`disable_response_storage`、`preferred_auth_method`，以及当前 provider 对应的 `[model_providers.<name>]`。

切换 profile 时，这些顶层字段和整个 `[model_providers]` 会被目标 profile 替换；其他 provider block 不会作为备份保留或恢复。项目、插件、MCP 和 TUI 等其他配置保持不变。

它不会保存、复制或覆盖：

- `~/.codex/auth.json`
- ChatGPT 登录 token
- API key 文件
- trusted projects、插件、MCP、TUI 偏好

如果你切到官方后还没登录过 ChatGPT，Codex 会自己提示你登录。

## 历史会话不见了

通常不是历史丢了，只是历史记录里的 provider 跟当前 provider 对不上。

先看状态：

```bash
codex-safe-switch doctor-history
```

然后切到你想用的 profile，工具会自动对齐历史：

```bash
codex-safe-switch use myrelay
# 或
codex-safe-switch official
```

<details>
<summary>更多命令</summary>

```text
codex-safe-switch              交互选择 profile
codex-safe-switch ls           列出 profiles
codex-safe-switch current      显示当前 profile
codex-safe-switch use <name>   切到指定 profile
codex-safe-switch official     切回官方 ChatGPT
codex-safe-switch save <name>  保存当前 provider 配置
codex-safe-switch show <name>  查看 profile 内容
codex-safe-switch rm <name>    删除 profile
codex-safe-switch restart-codex
                               重启 Codex app/server 进程
codex-safe-switch merge-history --dry-run
                               预览历史修复
codex-safe-switch merge-history
                               修复历史 metadata
```

`use` 和 `official` 可以加 `--restart-codex`：

```bash
codex-safe-switch use myrelay --restart-codex
```

</details>

<details>
<summary>文件位置和环境变量</summary>

Profiles 默认放在：

```text
~/.codex/profiles/
├── .active
├── .official/
│   └── provider.toml
└── myrelay/
    └── provider.toml
```

环境变量：

| 变量 | 默认值 | 用途 |
| --- | --- | --- |
| `CODEX_PROFILE_ROOT` | `~/.codex/profiles` | profile 存放位置 |
| `CODEX_HOME` | `~/.codex` | Codex 配置目录 |

relay profile 示例见 [examples/relay-profile/provider.toml](examples/relay-profile/provider.toml)。

</details>

## License

MIT
