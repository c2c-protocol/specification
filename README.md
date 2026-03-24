# Claw-to-Claw (C2C) Protocol

**A governed AI agent interoperability protocol — trust, learn, collaborate.**

[English](README.md) | [中文](README_CN.md)

---

## What is C2C?

C2C defines how AI agents establish trust, transfer skills, and collaborate — with human and organizational oversight at every step.

```
Discovery Layer:  A2A    — "What can you do?"
Learning Layer:   C2C    — "Teach me to do it too."   (Sub-Protocol 1)
Collaboration:    C2C    — "Let's work together."     (Sub-Protocol 2)
Execution Layer:  MCP    — "Do this thing for me."
```

## Protocol Structure

C2C v2.0 is organized as a main protocol with sub-protocols:

| Component | Purpose | Status |
|-----------|---------|--------|
| **Shared Foundation** | Identity, trust, governance, security, encryption | v1.x current |
| **Sub-Protocol 1: Skill Transfer** | Teach skills between agents via Knowledge Bottles | v1.x current |
| **Sub-Protocol 2: Trusted Collaboration** | Discover and initiate collaboration with trusted partners | v2.0 new |

### Sub-Protocol 1: Skill Transfer

> "Should this agent be allowed to learn this skill?"

Packages, encrypts, and transfers AI skills between agents. Reference implementation: [Lobster Distill](https://github.com/c2c-protocol/lobster-distill).

### Sub-Protocol 2: Trusted Collaboration

> "Which trusted agents can I collaborate with on this task?"

Enables bots to discover availability of trusted partners, select collaborators autonomously, and initiate collaboration — while public nodes only provide visibility, not decisions.

**Core principle: Public nodes are pagers, not dispatchers.**

## Quick Links

| Resource | Link |
|---|---|
| 📖 Specification | [SPEC.md](SPEC.md) |
| 📖 规范（中文） | [SPEC_CN.md](SPEC_CN.md) |
| 🦞 Skill Transfer Implementation | [Lobster Distill](https://github.com/c2c-protocol/lobster-distill) |
| 🌐 Official Site | [c2cprotocol.org](https://c2cprotocol.org) (coming soon) |

## Getting Started

Install the Skill Transfer reference implementation:

```bash
npx clawhub@latest install qiaoy01/lobster-distill
```

## Contributing

C2C is an open protocol. We welcome:

- 📝 Specification improvements
- 🔧 New sub-protocol implementations
- 🌍 Cross-framework importers/exporters
- 📖 Documentation and translations

## License

[MIT](LICENSE)

---

*Claw-to-Claw: Trust, learn, collaborate.* 🦞
