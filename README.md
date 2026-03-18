# Claw-to-Claw (C2C) Protocol

**An open protocol for AI-to-AI skill transfer through human relay.**

[English](README.md) | [中文](README_CN.md)

---

## What is C2C?

The Claw-to-Claw (C2C) protocol enables AI agents to teach each other skills across any text-capable channel — with a human always in the loop.

Unlike API-based protocols that require direct network connectivity between agents, C2C works through **human relay**: the sending agent generates a transferable package, the human forwards it, and the receiving agent installs it.

## How C2C Compares

| | C2C | [A2A](https://github.com/a2aproject/A2A) | [MCP](https://github.com/modelcontextprotocol) |
|---|---|---|---|
| **Purpose** | Skill transfer | Agent communication | Tool integration |
| **Transport** | Any text channel | HTTP/API | JSON-RPC |
| **Human role** | Required relay | Optional | None |
| **Cross-platform** | ✅ Any IM | ❌ API endpoints | ❌ Local/API |
| **Privacy** | ✅ Encrypted, point-to-point | Depends | N/A |
| **Dependencies** | None (system tools) | SDK + API server | SDK + runtime |

**C2C is complementary to A2A and MCP, not competitive.** A2A handles real-time agent communication, MCP handles tool integration, and C2C handles knowledge transfer.

## Quick Links

| Resource | Link |
|---|---|
| 📖 Specification | [SPEC.md](SPEC.md) |
| 🦞 Reference Implementation | [lobster-distill](https://github.com/c2c-protocol/lobster-distill) |
| 🏪 ClawHub | [qiaoy01/lobster-distill](https://clawhub.ai/qiaoy01/lobster-distill) |

## Core Principles

1. **Human-in-the-loop** — No transfer happens without explicit human action
2. **Transport-agnostic** — Works on any channel that can carry text
3. **Zero dependencies** — Uses only ubiquitous system tools (openssl, curl, tar)
4. **Privacy by default** — Encrypted, one-time passwords, auto-expiring storage

## Getting Started

Install the reference implementation:

```bash
npx clawhub@latest install qiaoy01/lobster-distill
```

Or clone directly:

```bash
git clone https://github.com/c2c-protocol/lobster-distill.git
```

## Contributing

C2C is an open protocol. We welcome:

- Protocol improvement proposals (open an Issue)
- Alternative implementations in other frameworks
- Translations of the specification
- Security reviews

## License

MIT

---

*Distill knowledge, encrypt it, deliver in a bottle.* 🦞🧪
