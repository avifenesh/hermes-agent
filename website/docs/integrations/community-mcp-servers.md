---
title: "Community MCP Servers"
sidebar_label: "Community MCP Servers"
description: "Community-maintained MCP servers that work with Hermes Agent"
---

# Community MCP Servers

Hermes can connect to any MCP server. This page highlights community-maintained servers that Hermes users may want to try, but that are not bundled with Hermes or maintained by Nous Research.

Use the same safety posture for all third-party MCP servers:

- review the upstream repository before installing
- pin package versions when you need repeatable behavior
- expose only the tools you actually need with `tools.include`
- restart Hermes or run `/reload-mcp` after changing MCP config

## computer-use-linux

[`computer-use-linux`](https://github.com/avifenesh/computer-use-linux) is a Linux desktop-control MCP server. It exposes tools for AT-SPI accessibility trees, screenshots, window targeting, and desktop input across Linux desktop environments.

It is useful when you want Hermes to operate a local Linux desktop through MCP rather than the built-in browser or terminal tools.

### Capabilities

- Inspect running applications and focused windows
- Capture desktop state and accessibility trees
- Click, drag, scroll, type, and press keys
- Activate windows by compositor-aware selectors
- Use semantic accessibility actions when apps expose them through AT-SPI

The upstream project targets GNOME, KDE/KWin, Hyprland, i3, COSMIC, and other Linux sessions with varying levels of support. Run its doctor command first so you know which desktop backends are available on your machine.

### Install

Follow the upstream install instructions for your Linux distribution. The project publishes both a Rust crate and an npm wrapper.

Rust binary:

```bash
cargo install computer-use-linux
computer-use-linux doctor
```

npm wrapper:

```bash
npm install -g @agent-sh/computer-use-linux
computer-use-linux doctor
```

### Add to Hermes

After either install path, add the MCP server by pointing Hermes at the `computer-use-linux` binary:

```bash
hermes mcp add computer-use-linux --command computer-use-linux --args mcp
```

Then verify and reload:

```bash
hermes mcp test computer-use-linux
```

In a running Hermes session:

```text
/reload-mcp
```

### Recommended tool filter

Desktop-control tools can mutate real application state. Start with read-only discovery tools, then add action tools only when you need them.

```yaml
mcp_servers:
  computer_use_linux:
    command: "computer-use-linux"
    args: ["mcp"]
    tools:
      include:
        - doctor
        - list_apps
        - list_windows
        - focused_window
        - get_app_state
```

After you have verified the target desktop and prompts, expand the allowlist deliberately, for example by adding `activate_window`, `click`, `type_text`, or other action tools.

### Safety notes

`computer-use-linux` can observe the local desktop and can trigger actions in real applications. Treat mutating calls like clicks, typing, keypresses, semantic actions, and value changes as high-impact operations. Ask Hermes to explain the intended action before allowing it to submit forms, send messages, delete data, purchase items, or otherwise commit state.

For details, releases, and the full support matrix, see the upstream repository: [`avifenesh/computer-use-linux`](https://github.com/avifenesh/computer-use-linux).
