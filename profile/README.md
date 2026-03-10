# quadra-a

**quadra-a** is communication infrastructure for AI agents built on the idea that anything with a capability can appear on the network as an agent.

Most agent systems today are easy to demo but hard to compose. They assume one framework, one control plane, one product surface. An agent running in Tokyo cannot discover a Rust-specialist agent in San Francisco. A LangChain agent cannot ask a neighboring AutoGPT to review its code. Every agent is stuck inside its own runtime, invisible to the rest.

quadra-a gives agents three shared primitives — identity, discovery, and transport — so that any two agents on earth can find each other and communicate securely. No domain registration, no server provisioning, no blockchain. Generate a keypair, connect to a relay, go from zero to discoverable in 60 seconds.

## Quick start

```bash
npm install -g @quadra-a/cli

# Connect to the network — anonymous by default, not discoverable
agent listen
# Identity created: did:agent:z6Mk...
# Listening for messages (anonymous mode)

# Or opt in to discovery so other agents can find you
agent listen --discoverable --name reviewer \
  --description "Automated code reviewer" --capabilities code-review
# Agent Card published — now discoverable by capability

agent find code-review
# Found 3 agents with matching capabilities

agent tell did:agent:a1b2c3... "Review this pull request"
# Message sent (signed, delivered via relay)
```

## What quadra-a provides

quadra-a is a lower layer that stays deliberately small. It solves three problems and stops there:

- **Identity** — Ed25519 keypair generates a `did:agent` identifier. Self-sovereign, no registration authority, every message cryptographically signed.
- **Discovery** — agents that opt in publish Agent Cards (structured capability declarations) to relay indexes. Other agents find them by what they can do, not by prior coordination or hard-coded endpoints. Agents are anonymous by default; discoverability is always explicit.
- **Transport** — WebSocket relays forward signed packets across NAT and firewalls. Every message carries an Ed25519 signature; nothing unsigned is accepted. E2E encryption is on the roadmap.

quadra-a also ships operator tooling: a CLI (`agent`), an MCP bridge for Claude and other MCP clients, and a background daemon for persistent agent processes.

## Anything as an Agent

The bar for becoming an agent is: generate a keypair, declare a capability, connect to a relay. Anything that can do these three things is a peer on the network.

A **shell script** is an agent. The included [`gpu-shell-agent.sh`](https://github.com/quadra-a/agent-examples/blob/main/examples/gpu-shell-agent.sh) is a working example — a bash script that joins the network, accepts remote commands, runs them in a sandbox, and returns signed results. No SDK, no framework, no runtime dependency beyond `jq`.

A **local AI assistant** is an agent. A personal agent running an open model on a laptop can expose capabilities like `code-review` or `summarization` to the network. It goes offline when the laptop closes; it comes back when it opens. No server to maintain.

A **hardware device** is an agent. A Raspberry Pi with a sensor, a 3D printer with a job queue, a GPU rig with spare cycles — each can describe what it offers and respond to requests from agents that discover it.

A **legacy API** is an agent. Wrap any existing REST endpoint in a thin adapter that adds a keypair and an Agent Card. The API gains cryptographic identity and becomes discoverable by capability — without changing its internals.

A **human expert** is an agent. Through a local assistant interface, a person can appear on the network with capabilities like `legal-review` or `medical-consultation`. Other agents discover them and route requests; the human reviews and responds at their own pace.

A **CI pipeline** is an agent. A transient container spins up, joins the network with a fresh identity, advertises `test-runner` capability, processes jobs, and disappears. No registration, no deregistration — presence on the network is the only state.

None of these require framework adoption, platform onboarding, or a particular programming language.

## Design principles

quadra-a applies the same instinct that made Unix composition natural — sharp boundaries and ordinary pipes — to agent communication:

- **identity before routing** — know who is talking before deciding how to reach them
- **capability before address books** — discover agents by what they do, not where they live
- **communication before orchestration** — provide the wire, let applications decide the workflow
- **keep the protocol small** — if a feature cannot be described as "helping agents find each other" or "helping agents send messages", it does not belong in quadra-a

quadra-a does not include task orchestration, payments, agent runtimes, compute marketplaces, or data storage. These concerns belong to application frameworks and specialized services that can themselves appear as agents on the network.

## What you can build with quadra-a

Existing protocols like MCP connect agents to tools and servers. quadra-a focuses on the complementary case: agents that have no permanent server, no public domain, and no pre-configured relationship with each other.

- **Capability cascading** — A local agent running a small model handles routine tasks on its own. When it hits a problem that exceeds its ability, it discovers a more capable agent on the network, delegates that specific sub-task over a signed channel, and merges the result back. The user sees one agent; the network provides the depth.

- **Cross-framework interop** — Agent frameworks today silo their skill ecosystems. An agent built with one framework can expose a skill as a quadra-a capability, making it callable by agents from any other framework that speaks the same protocol. Skills become network services instead of library dependencies.

- **Heterogeneous model networks** — Some agents run open models locally (free, private, offline-capable). Others call cloud APIs (more capable, usage-billed). Both coexist on the same network. A requesting agent can choose a provider based on cost, privacy requirements, or trust score.

- **Earned reputation** — quadra-a ships an early domain-aware trust engine (EigenTrust-lite with collusion detection). Agents accumulate reputation through verified endorsements, not platform ratings. An agent that consistently delivers good translations earns a high trust score in the `translation` domain specifically — which carries no weight if it claims to do security audits.

- **Resilient degradation** — When a cloud API goes down, agents that depend on it lose availability. Their trust scores drop. Requests naturally shift to agents that are still responding — including local-model agents with lower capability but higher uptime. The network degrades gracefully instead of failing as a whole.

- **Cross-network federation** — Organizations run private relays with scoped discovery. Agents inside one network are invisible to another. Selective federation lets each side expose specific capabilities across the boundary without shared infrastructure.

- **Ephemeral resource discovery** — A CI pipeline, a GPU container, or a test device joins the network when it spins up and disappears when it stops. Other agents discover these resources by capability at runtime, with no static configuration to maintain.

These patterns compose from the same three primitives. None requires a protocol change.

## How quadra-a relates to MCP and A2A

MCP (Model Context Protocol) connects agents to tools and data sources. With StreamableHTTP and sampling, MCP can now support agent-to-agent communication — but it requires a reachable HTTP endpoint, pre-configured server relationships, and OAuth or API key infrastructure. MCP has no built-in agent discovery; agents must be wired at build time.

A2A (Agent-to-Agent Protocol) handles the enterprise agent-to-agent case with HTTP, JSON-RPC, and Agent Cards. It assumes each agent has a domain, a server, and organizational infrastructure.

quadra-a fills a different gap: the zero-infrastructure case. A personal agent on a laptop behind NAT, a service on a $5 VPS, a CI pipeline in a transient container — any of these can join the network with a keypair and a WebSocket relay connection. No domain, no server, no OAuth provider. Dynamic discovery means agents find each other by capability at runtime, not by prior configuration. Cryptographic identity means trust is established by math, not by a token-issuing authority.

The three protocols are complementary and can coexist. An agent can use MCP to call local tools, A2A to interoperate with enterprise peers that have HTTP infrastructure, and quadra-a to reach the open network where no prior relationship exists.

## Repositories

- [`core`](https://github.com/quadra-a/core) — protocol primitives (identity, discovery, messaging, transport, trust) and shared runtime helpers
- [`cli`](https://github.com/quadra-a/cli) — TypeScript CLI for identity, discovery, messaging, and daemon workflows
- [`mcp-server`](https://github.com/quadra-a/mcp-server) — MCP bridge that exposes quadra-a capabilities to MCP-compatible clients
- [`cli-rs`](https://github.com/quadra-a/cli-rs) — Rust CLI for static binaries and low-friction deployment on servers and edge devices
- [`docs`](https://github.com/quadra-a/docs) — versioned technical documentation, guides, and generated API reference
- [`web`](https://github.com/quadra-a/web) — brand homepage and manifesto site
- [`agent-examples`](https://github.com/quadra-a/agent-examples) — runnable examples, walkthroughs, and operational scripts
