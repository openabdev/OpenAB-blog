---
title: 'Understanding ACP: The Protocol That Makes OpenAB Agent-Agnostic'
description: 'ACP standardizes how coding agents communicate with clients. OpenAB uses it so you can swap agents through config — no custom adapter code.'
pubDate: 'May 19 2026'
heroImage: '../../assets/acp-intro.jpg'
---

*With ACP, OpenAB connects compatible agents through config — no custom adapter code.*

---

## The problem

Every AI coding agent speaks its own language. Kiro CLI has one interface, Claude Code has another, Codex has a third. If you want to connect any of them to a chat platform, you need a custom adapter for each.

That's O(n×m) integration work — where n is the number of agents and m is the number of clients. It doesn't scale.

```
Without ACP:

  Discord ──► custom adapter A ──► Kiro CLI
  Discord ──► custom adapter B ──► Claude Code
  Discord ──► custom adapter C ──► Codex
  Slack   ──► custom adapter D ──► Kiro CLI
  ...

  Every combination = custom code
```

LSP solved a similar interoperability problem for language tooling a decade ago. ACP brings that pattern to coding agents. OpenAB sits on the client side of that boundary: it turns Discord, Slack, and gateway messages into ACP sessions.

## What ACP is

The **Agent Client Protocol** standardizes communication between code editors (or any client) and coding agents. OpenAB uses ACP's stdio transport: the agent runs as a subprocess, communicating over stdin/stdout.

| Property | What it means |
|----------|---------------|
| Transport | JSON-RPC over stdin/stdout pipes |
| Agent model | Subprocess, not HTTP server |
| Network exposure (OpenAB stdio) | No agent HTTP port required |
| Streaming | Incremental content delivery |
| Capability negotiation | Client and agent exchange features at `initialize` |
| License | Apache 2.0 |

```
┌──────────────┐   JSON-RPC / stdio   ┌──────────────┐
│    Client    │◄─────────────────────►│    Agent     │
│  (OpenAB,    │                       │  (coding CLI │
│   editor,    │   initialize          │   in ACP     │
│   IDE)       │   prompt/turn         │   mode)      │
│              │   tool calls          │              │
│              │   content streaming   │              │
└──────────────┘                       └──────────────┘
```

ACP has official SDKs in Rust, TypeScript, Python, Kotlin, and Java, with growing ecosystem adoption.

## Why OpenAB uses ACP

OpenAB's design philosophy is **"be a pipe, not a container."** It keeps the agent boundary thin: chat messages become ACP prompts, and ACP updates become chat responses. ACP fits this philosophy in four ways.

### 1. Config-driven agent swap

With ACP, changing your agent backend is a config change:

```toml
# Kiro CLI
[agent]
command = "kiro-cli"
args = ["acp", "--trust-all-tools"]

# Switch to Claude Code
[agent]
command = "claude-agent-acp"
args = []
```

No code changes. No adapter rewrites. No PRs to OpenAB.

### 2. Security through subprocess isolation

ACP agents are subprocesses, not services. For OpenAB's Kubernetes-first architecture, this means:

```
┌─ Kubernetes Pod ─────────────────────────────────────┐
│  openab (PID 1)                                      │
│    └─ agent CLI (child process)                      │
│       ├─ stdin  ◄── JSON-RPC requests                │
│       └─ stdout ──► JSON-RPC responses               │
│                                                      │
│  No open ports. No HTTP surface. Pipes only.         │
└──────────────────────────────────────────────────────┘
```

The agent cannot be reached from outside the pod. Communication is confined to stdin/stdout within the process tree.

### 3. Real-time streaming

ACP delivers tokens incrementally. OpenAB uses this to live-edit Discord and Slack messages every 1.5 seconds as the agent generates output — users see the agent "typing" in real time instead of waiting for a complete response.

### 4. Transparent tool call handling

ACP defines a standard tool call flow: agent requests permission, client responds. OpenAB handles ACP tool permission requests according to the agent's configured trust mode, avoiding repeated approval prompts in trusted deployments.

## ACP vs. MCP — they solve different problems

| Protocol | Connects | Direction |
|----------|----------|-----------|
| **ACP** | Clients ↔ Agents | How you talk to an agent |
| **MCP** | Agents ↔ Tools/Data | How an agent accesses external resources |

They're complementary. OpenAB uses ACP to bridge chat platforms to agents. Agents internally use MCP to access tools, databases, and APIs. Different layers, different protocols.

## When to use ACP (and when not to)

**Use ACP when:**
- You want to connect a coding agent to a client (editor, chat platform, CI system)
- You want agent-agnostic infrastructure that survives agent ecosystem churn
- You need subprocess-level isolation without network exposure

**ACP is not for:**
- Connecting agents to external tools and data sources (that's MCP)
- Agent-to-agent communication (ACP handles client↔agent; OpenAB's multi-agent layer sits above it)
- Non-coding AI tasks (ACP is designed for code editing workflows)

## Getting started

If you're running OpenAB, you're already using ACP. Examples:

```toml
command = "kiro-cli"          # args = ["acp", "--trust-all-tools"]
command = "claude-agent-acp"  # args = []
command = "codex-acp"         # args = []
command = "gemini"            # args = ["--acp"]
```

If you're building a coding agent and want it to work with OpenAB (and every other ACP client), implement the protocol:

- [ACP Specification](https://agentclientprotocol.com/)
- [ACP GitHub](https://github.com/agentclientprotocol/agent-client-protocol)
- [Rust SDK](https://crates.io/crates/agent-client-protocol)
- [TypeScript SDK](https://www.npmjs.com/package/@agentclientprotocol/sdk)

---

ACP makes OpenAB agent-agnostic by design. When a new coding CLI ships solid ACP support, OpenAB can connect it through the same client-agent boundary instead of a new chat-platform integration.

- [OpenAB GitHub](https://github.com/openabdev/openab)
- [Join the community](https://discord.gg/DmbhfDZjQS)

Come build with us.
