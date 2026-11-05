# Nib

Nib is a desktop design editor with agents built in. Draw and lay out screens, build components,
variables and prototypes, and let an AI agent (in the app, or Claude Code, Codex, Cursor and others
over MCP) edit the same file while you watch.

This repository holds Nib's downloads and update feed. **[Download the latest release](https://github.com/chenzizhen237-crypto/nib-releases/releases/latest).**

## Download

| Platform | File | Updates |
| --- | --- | --- |
| macOS, Apple Silicon | `Nib-<version>-arm64.dmg` | manual: download the new `.dmg` (see below) |
| Windows 10/11, x64 | `Nib-Setup-<version>.exe` | automatic |
| Linux, x86_64 | `Nib-<version>-x86_64.AppImage` | automatic |

Intel Macs and ARM Windows or Linux aren't built yet.

## Install

### macOS

1. Open the `.dmg` and drag **Nib** to **Applications**.
2. The app isn't notarized by Apple, so the first launch is blocked. Open it once with
   **right-click ▸ Open**, or allow it in **System Settings ▸ Privacy & Security ▸ Open Anyway**.
   If macOS says the app "is damaged", clear the download flag and open it again:

   ```bash
   xattr -dr com.apple.quarantine /Applications/Nib.app
   ```

### Windows

Run `Nib-Setup-<version>.exe`. It installs for your user and lets you pick the folder. The
installer isn't code-signed, so SmartScreen may warn: choose **More info ▸ Run anyway**.

### Linux

```bash
chmod +x Nib-*-x86_64.AppImage
./Nib-*-x86_64.AppImage
```

Some distributions need FUSE 2 for AppImages (`libfuse2` on Ubuntu 22.04 and later).

## Updates

Nib checks this repository when it starts and every 4 hours, downloads an update in the
background and offers to restart when it's ready. **Settings ▸ Updates** and
**Help ▸ Check for Updates…** show the same state.

- **Windows** and **Linux (AppImage)** update themselves.
- **macOS** can't apply updates in place, because macOS only installs signed updates and these
  builds aren't. Nib still tells you when a new version is out: download its `.dmg` and replace the
  app in Applications. Your files are kept.

## What's in it

- **Canvas and layout.** Frames, shapes, pen paths, text and icons; auto layout with hug and fill
  sizing; components with instances and overrides; variables and themes (light/dark and other
  modes).
- **Effects.** Gradients, image fills, shadows, layer and background blur, liquid glass and
  shader fills, drawn on the GPU (WebGPU), with a CPU renderer when no GPU is available.
- **Prototypes, slides and motion.** Link frames with interactions, play a flow in presentation
  mode, present frames as slides, and animate layers on a timeline.
- **Type.** Search Google Fonts by name or feel, compare fonts side by side, and apply pairings
  as variables.
- **Agents.** An agent panel (⌘I / Ctrl+I) that reads and edits the open file, with Anthropic,
  OpenAI or Google models and your own API key.
- **Files.** `.nib` files in a local workspace with tabs and autosave. Open or drop `.nib`, `.pen`,
  Figma `.fig` and Photoshop `.psd` files; export PNG, JPG, WebP, SVG and PDF, a Figma `.fig`, and
  code (React, HTML, SwiftUI, Compose).

## Use Nib from coding agents (MCP)

While Nib is open it serves an MCP endpoint at `http://localhost:37413/mcp` (streamable HTTP). Any
MCP client can read and edit your designs through it:

```bash
# Claude Code
claude mcp add --transport http nib http://localhost:37413/mcp
```

```toml
# Codex: ~/.codex/config.toml
[mcp_servers.nib]
url = "http://localhost:37413/mcp"
```

```json
// Cursor: ~/.cursor/mcp.json
{ "mcpServers": { "nib": { "url": "http://localhost:37413/mcp" } } }
```

The MCP panel in Nib shows the exact address if the port differs on your machine.

## Your files

Designs live in a workspace folder on your computer:

| Platform | Workspace |
| --- | --- |
| macOS | `~/Library/Application Support/Nib/workspace` |
| Windows | `%APPDATA%\Nib\workspace` |
| Linux | `~/.config/Nib/workspace` |

API keys for the in-app agent stay on your computer and go only to the model provider you chose.

## Reporting a problem

Open an [issue](https://github.com/chenzizhen237-crypto/nib-releases/issues) with your Nib version
(**Settings ▸ Updates**), your platform, and what you did. A screenshot or the file helps.

## About this repository

The source is in a separate, private repository. Each release is built here by
[`.github/workflows/release.yml`](.github/workflows/release.yml) for macOS, Windows and Linux into
one draft, which is published only when all three builds have uploaded. The app reads
`latest-mac.yml`, `latest.yml` and `latest-linux.yml` from the latest release to find updates.
