# codex-safe-switch

[![PyPI](https://img.shields.io/pypi/v/codex-safe-switch.svg)](https://pypi.org/project/codex-safe-switch/)
[![CI](https://github.com/kadaliao/codex-safe-switch/actions/workflows/ci.yml/badge.svg)](https://github.com/kadaliao/codex-safe-switch/actions/workflows/ci.yml)

[中文](README.md) | English

Switch Codex CLI between the official ChatGPT provider, third-party relays, and multiple API keys.

## 30 Seconds

[Install `uv`](https://docs.astral.sh/uv/getting-started/installation/) first, then install this tool:

```bash
uv tool install codex-safe-switch
codex-safe-switch
```

The first run imports the provider config slice from your current `~/.codex/config.toml`.

If the tool is already installed, upgrade it to the latest version with:

```bash
uv tool upgrade codex-safe-switch
```

After that, you mostly need three commands:

```bash
codex-safe-switch ls            # list profiles
codex-safe-switch use myrelay   # switch to a relay / custom provider
codex-safe-switch official      # switch back to official ChatGPT
```

## Save A New Profile

First, manually configure Codex until it works. For example, configure your relay and make sure `codex` runs.

Then save it:

```bash
codex-safe-switch save myrelay
```

Switch back to it later:

```bash
codex-safe-switch use myrelay
```

## First Run Behavior

| Current state | What happens |
| --- | --- |
| You are using official ChatGPT | Saved as `official` |
| You are using a relay / custom provider | Saved under the current provider name |
| You are logged into ChatGPT, but currently using a relay | The relay is saved first; run `codex-safe-switch official` later to go back |
| There is no `~/.codex/config.toml` | The tool asks you to configure Codex once first |

`codex-safe-switch official` does not require an existing `official` profile. If it is missing, the tool creates a default official config before switching.

## It Never Touches Login Data

This tool only saves and switches a provider config slice: `model`, `model_provider`, `model_reasoning_effort`, `model_reasoning_summary`, `model_verbosity`, `wire_api`, `disable_response_storage`, `preferred_auth_method`, and the active provider's `[model_providers.<name>]` block.

When switching profiles, these top-level fields and the entire `[model_providers]` table are replaced by the target profile. Other provider blocks are not backed up or restored. Other config, including projects, plugins, MCP, and TUI settings, stays unchanged.

It never saves, copies, or overwrites:

- `~/.codex/auth.json`
- ChatGPT login tokens
- API key files
- trusted projects, plugins, MCP config, or TUI preferences

If you switch to official before logging into ChatGPT on this machine, Codex will ask you to log in.

## History Disappeared?

Usually the history files are still there. The metadata just points at a different provider than the one currently active.

Inspect:

```bash
codex-safe-switch doctor-history
```

Then switch to the profile you want. The tool aligns history automatically:

```bash
codex-safe-switch use myrelay
# or
codex-safe-switch official
```

<details>
<summary>More Commands</summary>

```text
codex-safe-switch              interactive profile picker
codex-safe-switch ls           list profiles
codex-safe-switch current      show the current profile
codex-safe-switch use <name>   switch to a profile
codex-safe-switch official     switch to official ChatGPT
codex-safe-switch save <name>  save the current provider config
codex-safe-switch show <name>  show profile contents
codex-safe-switch rm <name>    delete a profile
codex-safe-switch restart-codex
                               restart Codex app/server processes
codex-safe-switch merge-history --dry-run
                               preview history repair
codex-safe-switch merge-history
                               repair history metadata
```

`use` and `official` accept `--restart-codex`:

```bash
codex-safe-switch use myrelay --restart-codex
```

</details>

<details>
<summary>Files And Env Vars</summary>

Profiles live here by default:

```text
~/.codex/profiles/
├── .active
├── .official/
│   └── provider.toml
└── myrelay/
    └── provider.toml
```

Environment variables:

| Var | Default | Purpose |
| --- | --- | --- |
| `CODEX_PROFILE_ROOT` | `~/.codex/profiles` | where profiles are stored |
| `CODEX_HOME` | `~/.codex` | Codex config directory |

See [examples/relay-profile/provider.toml](examples/relay-profile/provider.toml) for a relay profile example.

</details>

## License

MIT
