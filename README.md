# Claw-to-Claw (C2C) Protocol

**A governed AI skill distribution protocol — consent-based, certified, cross-framework.**

[English](README.md) | [中文](README_CN.md)

---

## What is C2C?

C2C answers a question no existing protocol addresses: **"Should this agent be allowed to learn this skill?"**

Unlike runtime protocols (MCP, A2A, ACP), C2C is about **permanent knowledge transfer** — one agent teaching another a capability it didn't have before, with human and organizational oversight at every step.

```
Discovery Layer:  A2A    — "What can you do?"
Learning Layer:   C2C    — "Teach me to do it too."
Execution Layer:  MCP    — "Do this thing for me."
```

## Protocol Stack

| Layer | Purpose | Status |
|-------|---------|--------|
| **Layer 4: Governance** | Policies, auto-approve rules, org management | v4.0 planned |
| **Layer 3: Identity & Trust** | Owner identity, skill certification | v3.0 planned |
| **Layer 2: Knowledge Bottle** | Portable skill format (instruction + code + API) | v2.0 planned |
| **Layer 1: Transfer** | Human relay, direct download, API | v1.x current |
| **Layer 0: Encryption** | AES-256-CBC, one-time passwords, 24h expiry | v1.x current |

## C2C vs Package Registries

| | ClawHub / npm / pip | C2C (c2cprotocol.org) |
|---|---|---|
| **Model** | Repository (files hosted) | Directory (metadata only) |
| **Privacy** | Public by default | Private by default |
| **Delivery** | Anyone can download | Owner must consent |
| **Scope** | Framework-specific | Cross-framework |
| **Analogy** | Docker Hub | Amazon Marketplace |

## Quick Links

| Resource | Link |
|---|---|
| 📖 Specification | [SPEC.md](SPEC.md) |
| 📖 规范（中文） | [SPEC_CN.md](SPEC_CN.md) |
| 🦞 Reference Implementation | [lobster-distill](https://github.com/c2c-protocol/lobster-distill) |
| 🌐 Official Site | [c2cprotocol.org](https://c2cprotocol.org) (coming soon) |

## Getting Started

Install the reference implementation:

```bash
npx clawhub@latest install qiaoy01/lobster-distill
```

## Contributing

C2C is an open protocol. We welcome:
- Protocol improvement proposals (open an Issue)
- Alternative implementations in other frameworks
- Cross-framework importers/exporters
- Security reviews

## License

MIT

---

*Distill knowledge, encrypt it, deliver in a bottle.* 🦞🧪
