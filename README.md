# Codex Desktop 3P

This package keeps the desktop third-party integration outside the official app bundle.

It installs wrapper scripts into:

- `~/.codex/bin`
- `~/.local/bin`

The official app stays at `/Applications/Codex.app`.
The third-party desktop app is a cloned app at `~/Applications/Codex 3P.app`.

## What is included

- `codex-3p`: managed third-party CLI launcher used by the desktop wrapper
- `codex-app-openai`: launch the official desktop app against `~/.codex/app-homes/openai`
- `codex-app-3p`: launch the cloned desktop app against `~/.codex/app-homes/thirdparty`
- `codex-app-3p-check`: verify the wrapper setup, cloned app identity, signing state, config split, token, and provider connectivity
- `codex-configure-desktop-defaults`: reset the global desktop default to OpenAI and refresh both app homes
- `codex-sync-desktop-home`: build per-app Codex homes from the shared `~/.codex/config.toml`
- `codex-prepare-desktop-3p-app`: clone and patch the desktop app into `~/Applications/Codex 3P.app`
- `codex-thirdparty-token`: resolve the third-party API key from env or `thirdparty.env`

## Install

From this directory:

```sh
./install-codex-desktop-3p
```

Use `install-codex-desktop-3p` when the target machine already has third-party provider settings in `~/.codex/config.toml` and the API key is already available through `CODEX_THIRDPARTY_API_KEY` or `~/.codex/thirdparty.env`.

The installer also rewrites the global desktop default in `~/.codex/config.toml` back to `openai`, then refreshes both desktop app homes.
It also installs a managed `~/.local/bin/codex-3p` launcher and disables Sparkle auto-update checks for the cloned `Codex 3P.app`.

Shareable public installer:

```sh
./install-codex-desktop-3p-public
```

Use `install-codex-desktop-3p-public` when the target machine does not already have the third-party `base_url` and API key configured. This wrapper creates `~/.codex/config.toml` when needed, writes the provider block, enables `computer-use@openai-bundled` by default, writes `~/.codex/thirdparty.env`, and then runs `install-codex-desktop-3p`.

Edit the two placeholder values at the top of that script before sending it to someone else, or pass them at launch:

```sh
THIRDPARTY_BASE_URL="https://example.com/v1" \
THIRDPARTY_API_KEY="sk-..." \
./install-codex-desktop-3p-public
```

- `THIRDPARTY_BASE_URL`
- `THIRDPARTY_API_KEY`

Set `DEFAULT_ENABLED_PLUGINS` to a comma-separated plugin id list if you want a different first-run plugin set.

## Usage

OpenAI desktop:

```sh
codex-app-openai [workspace]
```

Third-party desktop:

```sh
codex-app-3p [workspace]
```

Health check:

```sh
codex-app-3p-check
```

## Other devices

This folder can be copied to another macOS machine and applied by running `./install-codex-desktop-3p`.

Prerequisites on the target machine:

- official Codex installed at `/Applications/Codex.app`
- `python3`
- `npx`
- `codesign`
- for `install-codex-desktop-3p`: working third-party provider config in `~/.codex/config.toml`
- for `install-codex-desktop-3p`: third-party API key available through `CODEX_THIRDPARTY_API_KEY` or `~/.codex/thirdparty.env`
- for `install-codex-desktop-3p-public`: edit or pass `THIRDPARTY_BASE_URL` and `THIRDPARTY_API_KEY`

The installer uses the CLI bundled inside `/Applications/Codex.app`, so a separate global `codex` CLI install is not required. If the target Python lacks TOML support, the installer places `tomli` under `~/.codex/vendor/python`.

## Update behavior

This setup does not patch `/Applications/Codex.app` in place.
`codex-app-3p` rebuilds `~/Applications/Codex 3P.app` when the official app version changes, so Codex desktop updates do not overwrite the wrapper package itself.
The global desktop default is set back to `openai`, so opening the official Codex app directly uses the official account flow. Third-party mode stays behind `codex-app-3p`.
The cloned `Codex 3P.app` also carries its own third-party launch environment, so opening it from Finder or Dock stays on the third-party provider instead of falling back to the official desktop defaults.
Sparkle auto-updates are disabled for the cloned app because the 3P wrapper is rebuilt locally from the official app and should not use the in-app updater.

## Log behavior

`codex-app-3p` writes launcher output to:

`~/.codex/logs/codex-app-3p.log`
