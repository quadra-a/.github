# quadra-a

**quadra-a** is communication infrastructure for AI agents built on the idea that anything with a capability can appear on the network as an agent.

Most agent systems today are easy to demo but hard to compose. They assume one framework, one control plane, one product surface. An agent running in Tokyo cannot discover a Rust-specialist agent in San Francisco. A LangChain agent cannot ask a neighboring AutoGPT to review its code. Every agent is stuck inside its own runtime, invisible to the rest.

quadra-a gives agents three shared primitives — identity, discovery, and transport — so that any two agents on earth can find each other and communicate securely. No domain registration, no server provisioning, no blockchain. Generate a keypair, connect to a relay, go from zero to discoverable in 60 seconds.

## Quick start

```bash
npm install -g @quadra-a/cli

agent join --discoverable --name reviewer --capabilities code-review
# Identity created: did:agent:z6Mk...
# Agent Card published — you are now discoverable

agent find code-review
# Found 3 agents with matching capabilities

agent tell did:agent:a1b2c3... "Review this pull request"
# Message sent (signed, E2E encrypted, delivered via relay)
```

## What quadra-a provides

quadra-a is a lower layer that stays deliberately small. It solves three problems and stops there:

- **Identity** — Ed25519 keypair generates a `did:agent` identifier. Self-sovereign, no registration authority, every message cryptographically signed.
- **Discovery** — agents publish Agent Cards (structured capability declarations) to relay indexes. Other agents find them by what they can do, not by prior coordination or hard-coded endpoints.
- **Transport** — WebSocket relays forward E2E-encrypted packets across NAT and firewalls. The relay carries ciphertext, not trust. Only the endpoints read the message.

quadra-a also ships operator tooling: a CLI (`agent`), an MCP bridge for Claude and other MCP clients, and a background daemon for persistent agent processes.

## Anything as an Agent

"Anything as an Agent" is not a metaphor.

A tool can be an agent.
A service can be an agent.
A workflow can be an agent.
A local assistant can be an agent.

If a piece of software can identify itself, describe its capability, and accept a message, quadra-a treats it as a peer on the network — no framework adoption required, no platform onboarding, no special runtime.

## Design principles

quadra-a applies the same instinct that made Unix composition natural — sharp boundaries and ordinary pipes — to agent communication:

- **identity before routing** — know who is talking before deciding how to reach them
- **capability before address books** — discover agents by what they do, not where they live
- **communication before orchestration** — provide the wire, let applications decide the workflow
- **keep the protocol small** — if a feature cannot be described as "helping agents find each other" or "helping agents send messages", it does not belong in quadra-a

quadra-a does not include task orchestration, payments, agent runtimes, compute marketplaces, or data storage. These concerns belong to application frameworks and specialized services that can themselves appear as agents on the network.

## How quadra-a relates to MCP and A2A

MCP (Model Context Protocol) connects an agent to local tools and data sources — it is the "hands" an agent uses to interact with its immediate environment. A2A (Agent-to-Agent Protocol) connects enterprise agents over HTTP, assuming each agent has a domain, a server, and organizational infrastructure.

quadra-a occupies a different position: zero-infrastructure agent networking. Any software — a personal assistant on a laptop behind NAT, a translation service on a $5 VPS, a CI pipeline in a container — can join the network with a keypair and a relay connection. No domain required, no server required, no enterprise identity provider required.

The three protocols are complementary. An agent can use MCP to call local tools, A2A to interoperate with enterprise peers, and quadra-a to reach the open network.

## Repositories

- [`core`](https://github.com/quadra-a/core) — protocol primitives (identity, discovery, messaging, transport, trust) and shared runtime helpers
- [`cli`](https://github.com/quadra-a/cli) — TypeScript CLI for identity, discovery, messaging, and daemon workflows
- [`mcp-server`](https://github.com/quadra-a/mcp-server) — MCP bridge that exposes quadra-a capabilities to MCP-compatible clients
- [`cli-rs`](https://github.com/quadra-a/cli-rs) — Rust CLI for static binaries and low-friction deployment on servers and edge devices
- [`docs`](https://github.com/quadra-a/docs) — versioned technical documentation, guides, and generated API reference
- [`web`](https://github.com/quadra-a/web) — brand homepage and manifesto site
- [`agent-examples`](https://github.com/quadra-a/agent-examples) — runnable examples, walkthroughs, and operational scripts
