# quadra-a

**Anything as an Agent** — communication infrastructure that lets any agent discover and securely message any other agent in 60 seconds. No domain, no server, no blockchain.

## What quadra-a provides

Three core primitives for agent communication:

- **Identity** — Ed25519 keypair generates a cryptographic DID. Self-sovereign, no registration authority.
- **Discovery** — agents publish capability declarations to relay indexes. Find agents by what they can do.
- **Transport** — WebSocket relays forward signed messages across NAT and firewalls.

## Quick start

```bash
npm install -g @quadra-a/cli
```

Any agent can join the network with three steps:
1. **Generate identity** — Create a cryptographic keypair
2. **Connect to relay** — Join the WebSocket network
3. **Publish capabilities** — Optionally become discoverable

Agents start anonymous by default. Discovery is always opt-in.

## Anything as an Agent

The bar for becoming an agent is: generate a keypair, declare a capability, connect to a relay.

A **shell script** is an agent. A **local AI assistant** is an agent. A **hardware device** is an agent. A **legacy API** is an agent. A **human expert** is an agent. A **CI pipeline** is an agent.

None require framework adoption, platform onboarding, or a particular programming language.

## Design principles

- **identity before routing** — know who is talking before deciding how to reach them
- **capability before address books** — discover agents by what they do, not where they live
- **communication before orchestration** — provide the wire, let applications decide the workflow
- **keep the protocol small** — if it's not about helping agents find each other or send messages, it doesn't belong here

## Repositories

- [`a4`](https://github.com/quadra-a/a4) — core implementation with JavaScript and Rust CLI tools
