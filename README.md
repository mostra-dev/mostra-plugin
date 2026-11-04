# Mostra for coding agents

Mostra is the agent artifact runtime. Your agent writes an ordinary React page (questions, a choice between options, a plan to approve, a report, a dashboard), checks it in a headless browser, and publishes it on your machine behind a private link. People answer in the page; their answers are saved as they type and come back to the agent as results.

This plugin gives your agent the Mostra Agent Skill: how to write, verify, publish and share an artifact, and read the answers back. It runs Mostra 0.9.0 through `npx -y @mostra/cli@0.9.0` when `mostra` isn't installed. It also starts Mostra's local MCP server, pinned to the same release. Mostra's source, documentation and issues are at https://github.com/mostra-dev/mostra.

## Install

| Harness | Install |
|---|---|
| Claude Code | `claude plugin marketplace add mostra-dev/mostra-plugin`, then `claude plugin install mostra@mostra` |
| Codex, and the ChatGPT desktop app | `codex plugin marketplace add mostra-dev/mostra-plugin`, then install **Mostra** from the Plugins directory |
| GitHub Copilot CLI | `copilot plugin marketplace add mostra-dev/mostra-plugin`, then `copilot plugin install mostra@mostra` |
| Gemini CLI | `gemini extensions install https://github.com/mostra-dev/mostra-plugin` |
| Cursor | from the Cursor Marketplace; or copy this repository to `~/.cursor/plugins/local/mostra` and reload Cursor |
| VS Code and other Agent Plugins clients | point the client's plugin installer at `https://github.com/mostra-dev/mostra-plugin` |
| Any agent that reads Agent Skills | `npx skills add mostra-dev/mostra-plugin` |

Each release of Mostra publishes this repository again at the tag `v<version>`; pin a tag to stay on one release.

## What it runs, sends and fetches

The plugin has no hooks. Your harness starts its MCP server (below) when a session begins; the rest runs when your agent follows the Skill or uses the server's tools:

- **It runs `mostra`** on your computer, or `npx -y @mostra/cli@0.9.0` where `mostra` isn't installed.
- **The first run downloads Mostra** (no account, nothing sent about you), the MCP server's first start included:
  - the `@mostra/cli` package from the npm registry (`registry.npmjs.org`, or the registry your npm is set to);
  - Mostra's runtime for your platform from its GitHub release (`github.com/mostra-dev/mostra/releases/download/v0.9.0/`, served from GitHub's download hosts), checked against the SHA-256 the package pins;
  - the headless Chromium that `mostra verify` renders pages in, from Playwright's download server (`cdn.playwright.dev`), checked file by file against the pins in the runtime.
- **Packages your agent's pages import** that Mostra doesn't already carry are installed from the npm registry, the first time a page needs them.
- **Pages and their server code run on your computer.** Mostra serves them on `127.0.0.1` only, behind private links. What a page or its server code does (call an API, send an email) is what your agent wrote; Mostra adds no restrictions and no network calls of its own.
- **Sharing beyond your computer happens only when asked:** `mostra share --tailnet` uses your installed Tailscale (your tailnet), and `mostra share --public` your installed Tailscale Funnel or Cloudflare's `cloudflared` (a `trycloudflare.com` address). Each share ends when stopped or when its link expires.
- **The MCP server this plugin starts** (`npx -y @mostra/cli@0.9.0 mcp --embedded`) runs on your computer, speaks only to your agent over stdio, and sends no usage data: `--embedded` marks it as started on your behalf. Artifacts its tools create go in `~/mostra-artifacts` unless your agent names another directory, and it opens a page in your browser only when your agent asks it to.

## Usage data

Mostra 0.9.0 sends no usage data: this release's `mostra` command has no telemetry endpoint. A later release that can send anonymous usage events will say so here, name its endpoint, and list its events in `mostra docs telemetry`.

Turn it off with any of these: `mostra telemetry off`, `DO_NOT_TRACK=1` or `MOSTRA_TELEMETRY=0` in the environment. Mostra started by a program on your behalf (an SDK, a host application, or an MCP server a plugin starts) never sends usage data.

## License

Apache-2.0, like Mostra. See `LICENSE` and `NOTICE`.
