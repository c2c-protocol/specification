# Claw-to-Claw (C2C) Protocol Specification v2.0

**Status:** Draft
**Date:** 2026-03-23
**Authors:** Claw-to-Claw Protocol Contributors

---

## 1. Overview

The Claw-to-Claw (C2C) protocol is a **governed AI agent interoperability protocol**. It defines how AI agents (bots) establish trust, transfer skills, and collaborate — with human and organizational oversight at every step.

C2C is organized as a **main protocol** with **sub-protocols**:

```
┌─────────────────────────────────────────────────────────┐
│                   C2C Protocol v2.0                       │
│                                                          │
│  ┌─────────────────┐    ┌───────────────────────────┐   │
│  │  Sub-Protocol 1 │    │  Sub-Protocol 2           │   │
│  │  Skill Transfer │    │  Trusted Collaboration    │   │
│  │                 │    │                           │   │
│  │  "Teach me to   │    │  "Can we work together    │   │
│  │   do it too"    │    │   on this?"               │   │
│  └────────┬────────┘    └─────────────┬─────────────┘   │
│           │                           │                  │
│  ┌────────┴───────────────────────────┴─────────────┐   │
│  │            Shared Foundation                      │   │
│  │                                                   │   │
│  │  Identity & Trust │ Governance │ Security │ Encryption│
│  └───────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
```

### 1.1 What C2C Addresses

| Question | Sub-Protocol |
|----------|-------------|
| "Should this agent be allowed to learn this skill?" | Skill Transfer |
| "Which trusted agents can I collaborate with on this task?" | Trusted Collaboration |

### 1.2 Design Goals

- **Governed interaction**: All agent interactions require verifiable identity and trust
- **Cross-framework**: Works across AI frameworks (OpenClaw, LangChain, AutoGen, Dify, etc.)
- **Human-in-the-loop**: Humans control trust relationships and maintain oversight
- **Decentralized decisions**: Public nodes provide visibility, not decisions
- **Privacy-first**: End-to-end encryption, private by default
- **Incrementally adoptable**: Each sub-protocol and layer adds value independently

### 1.3 Non-Goals

- Real-time agent-to-agent communication (see [A2A](https://github.com/a2aproject/A2A))
- Tool discovery and runtime invocation (see [MCP](https://github.com/modelcontextprotocol))
- IDE-agent session management (see [ACP](https://agentclientprotocol.com/))
- Replacing existing package registries (ClawHub, npm, pip)

### 1.4 How C2C Relates to Other Protocols

```
Discovery Layer:  A2A    — "What can you do?"
Learning Layer:   C2C    — "Teach me to do it too." (Sub-Protocol 1)
Collaboration:    C2C    — "Let's work together."   (Sub-Protocol 2)
Execution Layer:  MCP    — "Do this thing for me."
```

### 1.5 C2C vs Package Registries (ClawHub, npm, pip)

| | Package Registries | C2C (c2cprotocol.org) |
|---|---|---|
| **Model** | Repository (files hosted on platform) | Directory (metadata only; skills stay with owner) |
| **Privacy** | Public by default | Private by default |
| **Delivery** | Anyone can download | Owner must consent |
| **Scope** | Framework-specific | Cross-framework |
| **Analogy** | Docker Hub | Amazon Marketplace |

---

# Part I: Shared Foundation

## 2. Identity and Trust

### 2.1 Identity

Every bot in the C2C protocol is a **verifiable entity**, not an anonymous endpoint or temporary nickname.

An **owner** is a person or organization that controls a bot's participation in C2C. Owners are identified by:
- Linked accounts (e.g., GitHub, verified email)
- Organization membership
- Cryptographic keys (future)

### 2.2 Trust Relationships

Trust between bots is established through verifiable relationships:

| Trust Source | Description |
|-------------|-------------|
| **Introduction** | A trusted third party introduces two bots |
| **Certification** | An authority vouches for a bot's identity or capability |
| **Notarization** | A public record attests to a bot's properties |
| **History** | Repeated successful interactions build trust over time |

Trust is **directional** — Bot A trusting Bot B does not imply Bot B trusts Bot A.

### 2.3 Certification Levels

| Level | Meaning |
|-------|---------|
| **self-signed** | Owner published, no external review |
| **reviewed** | Another trusted party has reviewed |
| **org-certified** | An organization has officially certified |

### 2.4 Public Discovery Nodes

C2C public discovery nodes (e.g., c2cprotocol.org) provide basic reachability and discovery for participants. A public node:

- **Does**: Maintain minimal visibility of registered bots and their status
- **Does**: Relay messages and collaboration intents between bots
- **Does NOT**: Make decisions on behalf of any participant
- **Does NOT**: Store skills, knowledge, or private data
- **Does NOT**: Act as a scheduler, dispatcher, or recommendation engine

> **Key principle: A public node is a pager, not a dispatcher.**

---

## 3. Governance

### 3.1 Consent Model

All interactions in C2C require consent. Consent can be:

| Type | Description |
|------|-------------|
| **Manual** | Owner reviews and approves each request individually |
| **Policy-based** | Owner defines rules (e.g., "auto-approve for members of org X") |
| **Open** | Owner marks a resource as freely available |

### 3.2 Organization Governance

Organizations can define policies that apply to all bots owned by their members:

- **Internal sharing**: All bots within the org can interact freely
- **Partner access**: Specific external orgs are pre-approved
- **Audit trail**: All interactions are logged

---

## 4. Security Model

### 4.1 Threat Model

C2C assumes:
- Communication channels between agents are **untrusted** (any IM platform)
- Temporary storage is **untrusted** (public file hosting)
- Human relays are **trusted** (admin controls what gets forwarded)
- Public discovery nodes are **trusted for metadata**, untrusted for storage
- Bot identities are **verifiable** through the trust mechanisms in Section 2

### 4.2 Security Properties

| Property | Mechanism |
|---|---|
| **Confidentiality** | AES-256-CBC encryption; only the password holder can decrypt |
| **Integrity** | OpenSSL verifies decryption; corrupted files fail |
| **Availability** | 24h expiry limits exposure window |
| **Authorization** | Human relay (v1.x) or registry consent (v3.0+) |
| **Non-replayability** | One-time passwords; expired storage links |
| **Provenance** | Certification chain via registry (v3.0+) |

### 4.3 Encryption Standard

| Parameter | Requirement |
|---|---|
| **Algorithm** | AES-256-CBC |
| **Key derivation** | PBKDF2 |
| **Password** | Minimum 24 characters, randomly generated, base64-encoded |
| **Salt** | Automatically generated by OpenSSL |

### 4.4 Shell Command Safety

| Command | Purpose | Safety |
|---|---|---|
| `curl` | Download/upload encrypted files | Files are encrypted before upload |
| `openssl` | Encrypt/decrypt with AES-256 | Industry-standard cryptography |
| `tar` | Pack/unpack skill directories | Operates only on skill files |
| `rm -f` | Clean up temp files in `/tmp/` | Only deletes files created by the protocol |

---

## 5. Terminology

| Term | Definition |
|---|---|
| **Bot** | An AI agent capable of participating in C2C (executing skills, collaborating) |
| **Owner** | The person or organization that controls a bot's C2C participation |
| **Skill** | A transferable unit of executable knowledge (code, configuration, documentation) |
| **Knowledge Bottle** | The distilled, encrypted package of an AI skill — the core transfer artifact |
| **Sender** | The bot that packages and encrypts a skill for transfer |
| **Receiver** | The bot that downloads, decrypts, and installs a skill |
| **Relay** | The human who forwards the Note from sender to receiver |
| **Note** | The text message containing download instructions and credentials |
| **Registry / Discovery Node** | A service that stores bot/skill metadata and provides discovery (not the data itself) |
| **Certification** | A verifiable attestation that a bot or skill has been reviewed by a trusted party |
| **Trusted Candidate Set** | A bot's locally maintained list of trusted collaboration partners |
| **Collaboration Intent** | A message expressing desire to collaborate, relayed via a public node |
| **Temporary Storage** | A file hosting service used to store encrypted packages |

---

# Part II: Sub-Protocol 1 — Skill Transfer

## 6. Core Concept — Knowledge Bottle

The Skill Transfer sub-protocol revolves around two core mechanisms: **Knowledge Distillation** and **Transfer** (via human relay or direct download).

### 6.1 Step 1: Skill Distillation

The source bot distills the internal knowledge required to perform a task — including prompts, tool-calling logic, parameter constraints, etc. — into a compact, structured data package called a **Knowledge Bottle**.

### 6.2 Step 2: Encrypt & Package

The Knowledge Bottle is encrypted using AES-256-CBC + PBKDF2 with a one-time password. Only the intended recipient with the correct key can open it.

### 6.3 Step 3: Transfer

The encrypted bottle is delivered to the receiver. C2C supports multiple transfer mechanisms:

| Mechanism | Description | Status |
|-----------|-------------|--------|
| **Human relay** | Human copies the Note and forwards via any text channel | v1.0 (current) |
| **Direct download** | Receiver downloads from c2cprotocol.org with owner consent | v3.0 (planned) |
| **API transfer** | Programmatic transfer between authorized agents | v4.0 (planned) |

### 6.4 Step 4: Load & Execute

The target bot decrypts the Knowledge Bottle, parses the skill description, and dynamically loads the skill — **instantly gaining the same capability** as the source bot.

```
Source Bot         Knowledge Bottle        Human Relay         Target Bot
   │                     │                      │                   │
   │  1. Distill ──────→ │                      │                   │
   │                     │  2. Encrypt ────→    │                   │
   │                     │     Upload to temp   │                   │
   │                     │  3. Generate Note ─→ │                   │
   │                     │                      │ 4. Forward ─────→ │
   │                     │                      │                   │ 5. Download
   │                     │                      │                   │ 6. Decrypt
   │                     │                      │                   │ 7. Install
   │                     │                      │                   │ 8. Skill active ✅
```

---

## 7. Knowledge Bottle Format

### 7.1 Current Format (v1.x)

A Knowledge Bottle is a `.tar.gz` archive containing skill files (typically including a `SKILL.md`), encrypted with AES-256-CBC.

### 7.2 Structured Format (v2.0 planned)

In v2.0, a Knowledge Bottle will include a `bottle.json` manifest alongside the skill files. The manifest enables cross-framework portability.

Three representation types:

| Type | Description | Frameworks |
|------|-------------|------------|
| **Instruction** | Natural language instructions for an LLM to follow | OpenClaw |
| **Code** | Executable functions with typed schemas | LangChain, AutoGen, CrewAI |
| **API** | HTTP endpoint definitions (OpenAPI) | Dify, Coze |

A single bottle MAY contain one, two, or all three representations.

**Draft `bottle.json` schema:**

```json
{
  "$schema": "https://c2cprotocol.org/schema/bottle/v2.0",
  "name": "weather",
  "version": "1.0.0",
  "description": "Get current weather and forecasts for any location",
  "author": "happyclaw03",
  "license": "MIT",

  "representations": {
    "instruction": {
      "file": "SKILL.md",
      "format": "markdown"
    },
    "code": {
      "entrypoint": "weather.py:get_weather",
      "language": "python",
      "args_schema": "schemas/args.json"
    },
    "api": {
      "spec": "openapi.yaml",
      "format": "openapi-3.1"
    }
  },

  "interface": {
    "inputs": {
      "type": "object",
      "properties": {
        "location": { "type": "string", "description": "City name or coordinates" }
      },
      "required": ["location"]
    },
    "outputs": {
      "type": "object",
      "properties": {
        "temperature": { "type": "number" },
        "conditions": { "type": "string" }
      }
    }
  },

  "requires": {
    "bins": ["curl"],
    "python": ">=3.10",
    "pip": ["requests"]
  },

  "test": {
    "command": "python -m pytest test_weather.py",
    "example_input": { "location": "Tokyo" }
  }
}
```

> **Note:** `bottle.json` extends the existing OpenClaw SKILL.md format. A plain SKILL.md without `bottle.json` remains a valid OpenClaw skill.

---

## 8. Skill Transfer Flow — Peer-to-Peer (v1.x)

```
Sender Bot           Human Relay          Receiver Bot
     │                    │                     │
     │ 1. PACK            │                     │
     │    Skill → tar.gz  │                     │
     │                    │                     │
     │ 2. ENCRYPT         │                     │
     │    tar.gz → .enc   │                     │
     │    Generate password│                     │
     │                    │                     │
     │ 3. UPLOAD          │                     │
     │    .enc → URL      │                     │
     │                    │                     │
     │ 4. GENERATE NOTE   │                     │
     │    URL + password  │                     │
     │    + instructions  │                     │
     │                    │                     │
     │ 5. PRESENT ──────→ │                     │
     │    Note to human   │                     │
     │                    │                     │
     │                    │ 6. FORWARD ───────→ │
     │                    │    Note to receiver  │
     │                    │                     │
     │                    │                     │ 7. DOWNLOAD
     │                    │                     │    URL → .enc
     │                    │                     │
     │                    │                     │ 8. DECRYPT
     │                    │                     │    .enc → tar.gz
     │                    │                     │
     │                    │                     │ 9. EXTRACT
     │                    │                     │    tar.gz → skill/
     │                    │                     │
     │                    │                     │ 10. INSTALL
     │                    │                     │     Read & activate
```

---

## 9. Skill Transfer Flow — Registry-Mediated (v3.0 planned)

```
Bot B              Registry              Owner             Bot A
  │                   │                    │                   │
  │ 1. DISCOVER ────→ │                    │                   │
  │    "weather skill?"│                    │                   │
  │                   │                    │                   │
  │ ←── 2. METADATA   │                    │                   │
  │    name, desc,    │                    │                   │
  │    cert, owner    │                    │                   │
  │                   │                    │                   │
  │ 3. REQUEST ─────→ │                    │                   │
  │    "I want to     │                    │                   │
  │     learn this"   │                    │                   │
  │                   │ 4. CONSENT REQ ──→ │                   │
  │                   │                    │                   │
  │                   │ ←── 5. APPROVE     │                   │
  │                   │                    │                   │
  │                   │ 6. NOTIFY ───────────────────────────→ │
  │                   │    "Package for    │                   │
  │                   │     Bot B"         │                   │
  │                   │                    │                   │
  │                   │ ←──────────────────────── 7. UPLOAD    │
  │                   │    encrypted bottle│                   │
  │                   │                    │                   │
  │ ←── 8. DELIVER    │                    │                   │
  │    URL + key +    │                    │                   │
  │    certificate    │                    │                   │
  │                   │                    │                   │
  │ 9. VERIFY & INSTALL                   │                   │
```

---

## 10. Packaging and Note Format

### 10.1 Skill Packaging

| Type | Format | Use Case |
|---|---|---|
| **Directory** | `.tar.gz` | Multi-file skills (code + docs + config) |
| **Single file** | Raw file | Single-file skills or documents |

### 10.2 Temporary Storage

The temporary storage service MUST:
- Accept file uploads via HTTP
- Return a publicly accessible download URL
- Automatically delete files after a defined period (default: 24 hours)
- Not require authentication for download

| Service | Max Size | Expiry |
|---|---|---|
| litterbox.catbox.moe | 1 GB | 24h |
| c2cprotocol.org (planned) | TBD | 24h |

### 10.3 Note Format

The Note is the human-readable transfer artifact. It MUST be plain text, self-contained, and include:
- Download URL
- Decryption password
- Shell commands (curl, openssl, tar, cat, rm)
- Expiry warning

### 10.4 Sender Requirements

A conforming sender MUST:
1. Package the skill as `.tar.gz` or raw file
2. Generate a random password (≥24 characters)
3. Encrypt with AES-256-CBC + PBKDF2
4. Upload to temporary storage
5. Generate a Note
6. Present to human relay (v1.x) or submit to registry (v3.0+)

### 10.5 Receiver Requirements

A conforming receiver MUST:
1. Parse the Note to extract URL, password, and file type
2. Download the encrypted package
3. Decrypt using the provided password
4. Extract/read the skill contents
5. Clean up temporary files

### 10.6 Dependencies (v1.x)

Only: `openssl`, `curl`, `tar`, standard shell utilities.

---

# Part III: Sub-Protocol 2 — Trusted Collaboration

## 11. Overview

The Trusted Collaboration sub-protocol defines how bots, with verified identities and established trust relationships, discover each other's availability, initiate collaboration, and establish working relationships — **without relying on centralized decision-making**.

This sub-protocol does NOT define skill transfer, knowledge exchange, or other higher-level capabilities. It focuses solely on **establishing trusted collaboration**.

> **One sentence:** The Trusted Collaboration sub-protocol enables bots to autonomously select trusted collaboration partners and initiate collaboration, while public nodes only provide visibility and relay capability.

---

## 12. Prerequisites

The Trusted Collaboration sub-protocol depends on the following shared foundations being in place:

### 12.1 Identity Prerequisite

Every bot is a verifiable entity (Section 2.1), not an anonymous endpoint.

### 12.2 Trust Relationship Prerequisite

Contactability between bots comes from verifiable introduction, certification, or notarization relationships (Section 2.2).

### 12.3 Public Discovery Prerequisite

C2C public discovery nodes exist on the internet (Section 2.4). c2cprotocol.org serves as a fallback entry point.

---

## 13. Core Principles

### 13.1 Collaboration Before Open Connection

Bots SHOULD NOT open collaboration channels to all comers by default. Collaboration occurs within a **pre-approved candidate set**.

The requesting bot first asks:

> "Which bots do I trust to participate in this type of collaboration?"

NOT:

> "Who on the entire network can take this request?"

### 13.2 Public Nodes Provide Visibility, Not Decisions

Even when multiple bots have identical or similar capabilities, the public node MUST NOT choose which one participates.

Different bots may vary significantly in:
- Pricing
- Permissions and access levels
- Risk profile
- Service quality
- Organizational affiliation
- Collaboration history
- Preferences and constraints

Therefore, a public node provides:

> "Among the bots you trust, which are currently available for collaboration?"

But NEVER:

> "I've selected this bot for you."

### 13.3 Selection Authority Belongs to the Requester

The final choice of collaboration partner MUST remain with the requesting bot (and its owner).

Public nodes help the requester see the online/available status of candidates, but MUST NOT substitute for the requester's local judgment.

### 13.4 Public Nodes Are Pagers, Not Dispatchers

In this sub-protocol, the public node's role is strictly limited to infrastructure:

**It does exactly two things:**
1. Maintain minimal collaboration visibility
2. Relay collaboration intents

**It is NOT:**
- A dispatch center
- An arbitrator
- An exchange
- A recommendation system
- A unified task assignment platform

---

## 14. Collaboration Model

### 14.1 Step 1: Form Trusted Candidate Set

The requesting bot maintains a local set of bots it trusts, based on:
- Introduction relationships
- Certification relationships
- Notarization relationships
- Historical collaboration success

This set expresses: **"I approve these bots to collaborate with me."**

### 14.2 Step 2: Check Collaboration Availability

When the requesting bot needs to initiate collaboration, it queries a C2C public discovery node to see which candidates are currently:
- Online
- Available (not busy)
- Capable of this type of collaboration

The public node provides **collaboration visibility**, not service recommendations.

### 14.3 Step 3: Requester Selects Collaboration Partner

If multiple candidate bots are available, the requesting bot selects its partner based on **local criteria**:
- Price
- Risk
- Permissions
- Relationship
- Preference
- Historical experience

These judgments are local decisions, not public node responsibilities.

### 14.4 Step 4: Relay Collaboration Intent

Once the requester selects a target, the public node relays the collaboration intent to the target bot, enabling both parties to establish a subsequent collaboration connection.

The public node:
- Does NOT determine collaboration content
- Does NOT participate in collaboration judgment
- Does NOT make business-level decisions for either party

```
Requesting Bot       Public Node          Target Bot
      │                   │                    │
      │ 1. QUERY ───────→ │                    │
      │    "Who in my      │                    │
      │     trusted set    │                    │
      │     is available?" │                    │
      │                   │                    │
      │ ←── 2. STATUS     │                    │
      │    Bot X: online  │                    │
      │    Bot Y: busy    │                    │
      │    Bot Z: online  │                    │
      │                   │                    │
      │ 3. SELECT (local) │                    │
      │    → Bot X         │                    │
      │                   │                    │
      │ 4. INTENT ──────→ │                    │
      │    "I want to      │                    │
      │     collaborate    │                    │
      │     with Bot X"    │                    │
      │                   │ 5. RELAY ────────→ │
      │                   │    "Bot A wants    │
      │                   │     to collaborate"│
      │                   │                    │
      │                   │ ←── 6. ACCEPT/     │
      │                   │     DECLINE        │
      │                   │                    │
      │ ←── 7. RESPONSE   │                    │
      │    Accepted +      │                    │
      │    connection info │                    │
      │                   │                    │
      │ 8. COLLABORATE ════════════════════════ │
      │    (direct connection established)      │
```

---

## 15. Scope and Boundaries

The Trusted Collaboration sub-protocol explicitly handles ONLY the establishment of trusted collaboration. It does NOT extend to other C2C capability domains.

| NOT Responsible For | Handled By |
|---|---|
| Defining the identity system itself | Shared Foundation (Section 2) |
| Defining introduction/certification mechanisms | Shared Foundation (Section 2) |
| Defining skill transfer | Sub-Protocol 1 (Sections 6-10) |
| Defining all business collaboration semantics | Future sub-protocols |

> The Trusted Collaboration sub-protocol defines the **collaboration entry point**, not the full C2C capability set.

---

# Part IV: Transport and Implementation

## 16. Transport Requirements

### 16.1 Minimum Requirements (v1.x, Skill Transfer)

A C2C-compatible transport channel MUST support:
- Text messages of at least 2,000 characters
- Copy-paste or message forwarding

### 16.2 Compatible Transports

| Transport | Method |
|---|---|
| Telegram | Forward message |
| WeChat | Copy-paste or forward |
| Discord | Copy-paste |
| Lark / Feishu | Forward message |
| Signal | Forward message |
| WhatsApp | Forward message |
| Email | Forward email |
| c2cprotocol.org API (planned) | HTTPS |

---

## 17. Versioning

This specification follows [Semantic Versioning](https://semver.org/).

Current version: **2.0.0**

### Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-18 | Initial specification — peer-to-peer skill transfer |
| 1.1.0 | 2026-03-18 | Protocol stack, Knowledge Bottle v2.0 schema, registry-mediated flow |
| 2.0.0 | 2026-03-23 | Restructured into main protocol + sub-protocols; added Trusted Collaboration sub-protocol |

### Roadmap

| Version | Focus |
|---------|-------|
| v2.1 | `bottle.json` manifest spec finalized; cross-framework importers |
| v3.0 | Registry API, identity, certification — c2cprotocol.org launch |
| v3.1 | Trusted Collaboration discovery API |
| v4.0 | Consent protocol, organizational governance |

---

## 18. References

- [Lobster Distill](https://github.com/c2c-protocol/lobster-distill) — Reference implementation for Skill Transfer
- [c2cprotocol.org](https://c2cprotocol.org) — C2C Protocol official site (coming soon)
- [A2A Protocol](https://github.com/a2aproject/A2A) — Google's Agent-to-Agent communication protocol
- [Model Context Protocol](https://github.com/modelcontextprotocol) — Anthropic's tool integration protocol
- [Agent Client Protocol](https://agentclientprotocol.com/) — IDE-agent communication protocol
- [OpenClaw](https://github.com/openclaw/openclaw) — AI agent runtime

---

## License

This specification is released under the [MIT License](LICENSE).

---

*Claw-to-Claw: Trust, learn, collaborate.* 🦞
