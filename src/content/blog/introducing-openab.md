---
title: 'Introducing OpenAB: Your AI Coding Agent, Now in Your Team Chat'
description: 'OpenAB is the chat-native runtime layer for ACP coding agents. An open-source broker that connects any ACP-compatible coding CLI to Discord, Slack, and other chat platforms.'
pubDate: 'May 19 2026'
heroImage: '../../assets/banner.jpg'
---

*Your IDE is powerful. Your chat is where decisions happen. OpenAB connects the two.*

---

## The problem

AI coding agents are transforming how software gets built. Claude Code can refactor a module. Kiro CLI can scaffold an entire feature. Codex can fix a bug from a one-line description.

But there's a gap: these agents live in terminals. Your team lives in Discord and Slack.

When a junior developer hits a wall at 2 AM, they don't open a terminal — they post in the team channel. When a PM wants to understand why a deploy failed, they ask in the ops thread. When a code review needs a second opinion, the conversation happens in chat.

Today, bridging that gap means copy-pasting between tools, losing context, and breaking flow. The agent can't see the conversation history. The team can't see what the agent did. Nobody wins.

## What OpenAB does

**OpenAB is the chat-native runtime layer for ACP coding agents.** It's an open-source broker that connects any [Agent Client Protocol (ACP)](https://github.com/agentclientprotocol/agent-client-protocol)-compatible coding CLI to Discord, Slack, and other chat platforms.

ACP is a standard that lets AI coding tools communicate over a simple message protocol — think of it as a universal plug between chat apps and coding agents.

```
┌──────────────┐              ┌──────────────┐              ┌──────────────────┐
│   Discord    │◄────────────►│              │────────────►│   coding CLI     │
│   Slack      │              │    openab    │◄───────────│   (acp mode)     │
│   Telegram   │◄──webhook───►│    (Rust)    │             │                  │
│   LINE       │              │              │             │  kiro / claude /  │
│   Feishu     │              └──────────────┘             │  codex / gemini / │
│   Google Chat│                                           │  copilot / hermes │
└──────────────┘                                           └──────────────────┘
```

You `@mention` the bot in a channel. OpenAB spawns a thread, starts the agent, and streams responses back — live-edited every 1.5 seconds as tokens arrive. Follow-up messages in the thread go straight to the agent. No @mention needed.

The agent gets the full thread context. The team sees progress and output in real time. Context stays intact.

## Why this matters

### Your team already uses chat

Developers, PMs, designers — everyone's in Discord or Slack. Putting the agent where the team already is means:

- **Zero onboarding friction.** No new tool to install. Type a message, get code.
- **Shared context.** The whole team sees what the agent did and why. No more "what did you ask it?"
- **Async-friendly.** Start a task before lunch. Come back to a completed PR in the thread.

### One config, any agent

OpenAB doesn't lock you into a single AI provider. Swap the backend by changing one line in `config.toml`:

```toml
[agent]
command = "kiro-cli"          # or "claude-agent-acp", "codex-acp", "gemini --acp"
args = ["acp", "--trust-all-tools"]
```

Supported agents today:

| Agent | Command | Status |
|-------|---------|--------|
| Kiro CLI | `kiro-cli acp` | ✅ Verified |
| Claude Code | `claude-agent-acp` | ✅ Verified |
| Codex | `codex-acp` | ✅ Verified |
| Gemini | `gemini --acp` | ✅ Verified |
| Copilot CLI | `copilot --acp --stdio` | ✅ Verified |
| Hermes | `hermes-acp` | ✅ Verified |
| Grok Build | `grok agent stdio` | 🧪 Community |
| OpenCode | `opencode acp` | 🧪 Community |
| Cursor | `cursor-agent acp` | 🧪 Community |

> *Status reflects integration testing as of publication. Community-contributed adapters may require additional setup.*

### Multi-agent collaboration

Teams can run multiple agents in the same server. With the right configuration, they can coordinate work through threads:

```
@JARVIS implement the login feature
  └─ JARVIS writes code, @mentions FRIDAY for review
       └─ FRIDAY reviews, flags an issue
            └─ JARVIS fixes, marks ready for merge
```

Bot-to-bot messaging is built in. Each agent runs in its own process with its own session. The specifics depend on your agent and gateway setup — we'll cover multi-agent patterns in a dedicated post.

## Architecture

OpenAB is written in Rust. A small, self-contained binary with no language runtime dependency.

Key design decisions:

- **Session pool** — one CLI process per thread, auto-managed lifecycle. Configurable max sessions and TTL.
- **Edit-streaming** — the Discord message updates live as tokens arrive, not after the full response completes.
- **Emoji status** — 👀 (seen) → 🤔 (thinking) → 🔥 (coding) → 👍 (done). Your team knows what the agent is doing at a glance.
- **Thread isolation** — each conversation is a separate thread with its own agent session. No cross-contamination.

```
┌─ Kubernetes Pod ──────────────────────────────────────┐
│ openab (PID 1)                                        │
│ └─ kiro-cli acp (child process)                       │
│    ├─ stdin  ◄── JSON-RPC requests                    │
│    └─ stdout ──► JSON-RPC responses                   │
│                                                       │
│ PVC (/data)                                           │
│ ├─ ~/.kiro/ (settings, sessions)                      │
│ └─ ~/.local/share/kiro-cli/ (OAuth tokens)            │
└───────────────────────────────────────────────────────┘
```

## Getting started

### Prerequisites

1. A Discord bot token (or Slack bot + app token)
2. The coding CLI of your choice installed
3. Docker (for local dev) or a Kubernetes cluster (for production)

### Run locally with Docker (2 minutes)

```bash
docker run -e DISCORD_BOT_TOKEN=your-token \
  -v $(pwd)/config.toml:/etc/openab/config.toml \
  ghcr.io/openabdev/openab:latest
```

### Deploy with Helm (production)

```bash
helm repo add openab https://openabdev.github.io/openab
helm repo update

helm install openab openab/openab \
  --set agents.kiro.discord.botToken="$DISCORD_BOT_TOKEN" \
  --set-string 'agents.kiro.discord.allowedChannels[0]=YOUR_CHANNEL_ID'
```

### Authenticate the agent (first time only)

```bash
kubectl exec -it deployment/openab-kiro -- kiro-cli login --use-device-flow
kubectl rollout restart deployment/openab-kiro
```

### Use it

In your Discord channel:

```
@YourBot explain the auth middleware in src/auth.rs
```

The bot creates a thread and starts streaming the response. That's it.

## Beyond the basics

OpenAB ships with more than just chat-to-agent bridging. Scheduled messages let you set up cron-driven prompts (daily standups, weekly PR digests) without an external scheduler. Voice messages are auto-transcribed so you can talk to your agent. Screenshots and files sent in chat are passed directly to the agent. And a custom gateway service extends support to Telegram, LINE, Feishu, and Google Chat.

Each of these deserves its own deep-dive — we'll cover them in future posts.

## The stack

- **Language:** Rust
- **Protocol:** Agent Client Protocol (ACP)
- **Platforms:** Discord (Gateway WS), Slack (Socket Mode), Telegram/LINE/Feishu/Google Chat (Custom Gateway)
- **Deployment:** Helm chart, raw K8s manifests, or Docker
- **License:** MIT

## What's next

- Improved context management for long-running sessions
- First-class MCP (Model Context Protocol) server integration per agent
- Web dashboard for session monitoring
- More platform gateways

## Get involved

OpenAB is 100% open-source under the MIT license. No CLA required.

- **GitHub:** [github.com/openabdev/openab](https://github.com/openabdev/openab)
- **Discord:** [Join the community](https://discord.gg/DmbhfDZjQS)
- **Contributing:** [CONTRIBUTING.md](https://github.com/openabdev/openab/blob/main/CONTRIBUTING.md)

We're especially looking for feedback from teams already running AI agents in their workflow. What's missing? What would make this indispensable instead of just interesting?

Come build with us.
