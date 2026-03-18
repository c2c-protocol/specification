# Claw-to-Claw (C2C) Protocol Specification v1.1

**Status:** Draft
**Date:** 2026-03-18
**Authors:** Claw-to-Claw Protocol Contributors

---

## 1. Overview

The Claw-to-Claw (C2C) protocol is a **governed AI skill distribution protocol**. It defines how AI agents discover, request, verify, and learn skills from each other — with human and organizational oversight at every step.

C2C answers a question no existing protocol addresses:

> **"Should this agent be allowed to learn this skill?"**

Unlike runtime protocols (MCP for tool calling, A2A for agent communication, ACP for IDE-agent interaction), C2C is about **permanent knowledge transfer** — one agent teaching another a capability it didn't have before.

### 1.1 Protocol Stack

```
┌─────────────────────────────────────────────────────┐
│              C2C Protocol Stack                      │
│                                                      │
│  Layer 4: GOVERNANCE (v4.0 planned)                  │
│  └─ Policies, auto-approve rules, org management    │
│                                                      │
│  Layer 3: IDENTITY & TRUST (v3.0 planned)            │
│  └─ Owner identity, skill certification, org verify  │
│                                                      │
│  Layer 2: KNOWLEDGE BOTTLE FORMAT (v2.0 planned)     │
│  └─ bottle.json manifest, multi-representation,      │
│     JSON Schema interfaces                           │
│                                                      │
│  Layer 1: TRANSFER (v1.x current)                    │
│  └─ Human relay (Lobster Distill)                    │
│  └─ Direct download (c2cprotocol.org, planned)       │
│                                                      │
│  Layer 0: ENCRYPTION (v1.x current)                  │
│  └─ AES-256-CBC, one-time passwords, 24h expiry      │
└─────────────────────────────────────────────────────┘
```

### 1.2 Design Goals

- **Governed distribution**: Skills are private by default; transfer requires owner consent
- **Cross-framework**: Skills are portable across AI frameworks (OpenClaw, LangChain, AutoGen, Dify, etc.)
- **Human-in-the-loop**: Humans control all transfers, maintaining oversight and trust
- **Transport-agnostic**: Works on any channel — IM, email, API, or human relay
- **Privacy-first**: End-to-end encryption with one-time passwords
- **Zero dependencies** (Layer 1): Uses only ubiquitous system tools
- **Incrementally adoptable**: Each layer adds value independently

### 1.3 Non-Goals

- Real-time agent-to-agent communication (see [A2A](https://github.com/a2aproject/A2A))
- Tool discovery and runtime invocation (see [MCP](https://github.com/modelcontextprotocol))
- IDE-agent session management (see [ACP](https://agentclientprotocol.com/))
- Replacing existing package registries (ClawHub, npm, pip)

### 1.4 How C2C Relates to Other Protocols

| Protocol | Purpose | Layer | C2C Relationship |
|----------|---------|-------|------------------|
| **MCP** | Tool calling | Execution | A learned skill may register as an MCP tool |
| **A2A** | Agent communication | Discovery | Agents may discover skills via A2A, then transfer via C2C |
| **ACP** | IDE-agent interaction | Session | Orthogonal — different use case |
| **ClawHub** | Skill repository (public) | Distribution | Complementary — C2C adds private, governed distribution |

```
Discovery Layer:  A2A    — "What can you do?"
Learning Layer:   C2C    — "Teach me to do it too."
Execution Layer:  MCP    — "Do this thing for me."
```

### 1.5 C2C vs Package Registries (ClawHub, npm, pip)

| | Package Registries | C2C (c2cprotocol.org) |
|---|---|---|
| **Model** | Repository (files hosted on platform) | Directory (metadata only; skills stay with owner) |
| **Privacy** | Public by default | Private by default |
| **Delivery** | Anyone can download | Owner must consent to each transfer |
| **Scope** | Framework-specific | Cross-framework |
| **Target** | Open-source community | Individuals + enterprises + organizations |
| **Analogy** | npm / Docker Hub | DNS / Amazon Marketplace |

**These are complementary, not competing.** A skill can be public on ClawHub AND registered on c2cprotocol.org for governed cross-framework distribution.

---

## 2. Core Concept — Knowledge Bottle

The C2C protocol revolves around two core mechanisms: **Knowledge Distillation** and **Human Relay**. The reference implementation is called **Lobster Distill**.

### 2.1 Step 1: Skill Distillation

The source AI distills the internal knowledge required to perform a task — including prompts, tool-calling logic, parameter constraints, etc. — into a compact, structured data package called a **Knowledge Bottle**.

### 2.2 Step 2: Encrypt & Package

The Knowledge Bottle is encrypted using AES-256-CBC + PBKDF2 with a one-time password. Only the intended recipient with the correct key can open it.

### 2.3 Step 3: Transfer

The encrypted bottle is delivered to the receiver. C2C supports multiple transfer mechanisms:

| Mechanism | Description | Status |
|-----------|-------------|--------|
| **Human relay** | Human copies the Note and forwards via any text channel | v1.0 (current) |
| **Direct download** | Receiver downloads from c2cprotocol.org with owner consent | v3.0 (planned) |
| **API transfer** | Programmatic transfer between authorized agents | v4.0 (planned) |

### 2.4 Step 4: Target AI Load & Execute

The target AI decrypts the Knowledge Bottle, parses the skill description, and dynamically loads the skill — **instantly gaining the same capability** as the source AI.

```
Source AI          Knowledge Bottle        Human Relay         Target AI
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

## 3. Terminology

| Term | Definition |
|---|---|
| **Agent** | An AI system capable of executing skills (e.g., an OpenClaw instance) |
| **Skill** | A transferable unit of executable knowledge (code, configuration, documentation) |
| **Knowledge Bottle** | The distilled, encrypted package of an AI skill — the core transfer artifact in C2C |
| **Sender** | The agent that packages and encrypts a skill for transfer |
| **Receiver** | The agent that downloads, decrypts, and installs a skill |
| **Owner** | The person or organization that controls a skill's distribution |
| **Relay** | The human who forwards the Note from sender to receiver |
| **Note** | The text message containing download instructions and credentials |
| **Registry** | A directory service that stores skill metadata and certifications (not the skills themselves) |
| **Certification** | A verifiable attestation that a skill has been reviewed by a trusted party |
| **Temporary Storage** | A file hosting service used to store the encrypted package |

---

## 4. Knowledge Bottle Format

### 4.1 Current Format (v1.x)

In v1.x, a Knowledge Bottle is a `.tar.gz` archive containing skill files (typically including a `SKILL.md`), encrypted with AES-256-CBC.

### 4.2 Structured Format (v2.0 planned)

In v2.0, a Knowledge Bottle will include a `bottle.json` manifest alongside the skill files. The manifest enables cross-framework portability.

A Knowledge Bottle supports **three representation types**, reflecting the three major approaches to AI skills:

| Type | Description | Frameworks |
|------|-------------|------------|
| **Instruction** | Natural language instructions for an LLM to follow | OpenClaw |
| **Code** | Executable functions with typed schemas | LangChain, AutoGen, CrewAI |
| **API** | HTTP endpoint definitions (OpenAPI) | Dify, Coze |

A single bottle MAY contain one, two, or all three representations. The receiving framework selects whichever mode it supports.

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

> **Note:** `bottle.json` extends the existing OpenClaw SKILL.md format. A plain SKILL.md without `bottle.json` remains a valid OpenClaw skill. `bottle.json` is an opt-in superset for cross-framework portability.

---

## 5. Protocol Flow — Peer-to-Peer (v1.x)

The current protocol flow uses human relay for transfer:

```
Sender Agent         Human Relay          Receiver Agent
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

## 6. Protocol Flow — Registry-Mediated (v3.0 planned)

Future versions will support registry-mediated transfer with consent:

```
Agent B            Registry              Owner             Agent A
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
  │                   │    "Agent B wants  │                   │
  │                   │     your skill"    │                   │
  │                   │                    │                   │
  │                   │ ←── 5. APPROVE     │                   │
  │                   │    (or auto-rule)  │                   │
  │                   │                    │                   │
  │                   │ 6. NOTIFY ───────────────────────────→ │
  │                   │    "Package and    │                   │
  │                   │     upload for     │                   │
  │                   │     Agent B"       │                   │
  │                   │                    │                   │
  │                   │ ←──────────────────────── 7. UPLOAD    │
  │                   │    encrypted bottle│                   │
  │                   │    + bottle hash   │                   │
  │                   │                    │                   │
  │ ←── 8. DELIVER    │                    │                   │
  │    URL + key +    │                    │                   │
  │    certificate    │                    │                   │
  │                   │                    │                   │
  │ 9. DOWNLOAD       │                    │                   │
  │ 10. VERIFY CERT   │                    │                   │
  │ 11. DECRYPT       │                    │                   │
  │ 12. INSTALL ✅    │                    │                   │
```

Key differences from v1.x:
- **Discovery**: Agent B can search the registry for skills
- **Consent**: Owner explicitly approves (or sets auto-approve policies)
- **Certification**: Receiver verifies the skill's certificate against the registry
- **Privacy**: Skills remain with the owner until consent is given

---

## 7. Package Format

### 7.1 Skill Packaging

A skill MUST be packaged as one of:

| Type | Format | Use Case |
|---|---|---|
| **Directory** | `.tar.gz` | Multi-file skills (code + docs + config) |
| **Single file** | Raw file | Single-file skills or documents |

For directory packaging:
```bash
tar czf <name>.tar.gz -C <skill-directory> .
```

### 7.2 Encryption

All packages MUST be encrypted before upload.

| Parameter | Requirement |
|---|---|
| **Algorithm** | AES-256-CBC |
| **Key derivation** | PBKDF2 |
| **Password** | Minimum 24 characters, randomly generated, base64-encoded |
| **Salt** | Automatically generated by OpenSSL |

Encryption command:
```bash
openssl enc -aes-256-cbc -pbkdf2 -salt -in <file> -out <file>.enc -k <password>
```

Decryption command:
```bash
openssl enc -aes-256-cbc -d -pbkdf2 -in <file>.enc -out <file> -k <password>
```

### 7.3 Password Generation

Passwords MUST be:
- At least 24 characters long
- Randomly generated using a cryptographically secure source
- Single-use (one password per transfer)

Reference implementation:
```bash
openssl rand -base64 24
```

---

## 8. Temporary Storage

### 8.1 Requirements

The temporary storage service MUST:
- Accept file uploads via HTTP
- Return a publicly accessible download URL
- Automatically delete files after a defined period
- Not require authentication for download

### 8.2 Recommended Services

| Service | Max Size | Expiry | Upload Method |
|---|---|---|---|
| litterbox.catbox.moe | 1 GB | 24h | `multipart/form-data` |
| c2cprotocol.org (planned) | TBD | 24h | C2C Registry API |

### 8.3 Expiry

Files SHOULD have a maximum lifetime of **24 hours**. The Note MUST inform the receiver of the expiry window.

---

## 9. Note Format

The Note is the human-readable transfer artifact. It MUST be plain text, readable by both humans and AI agents.

### 9.1 Structure

A Note MUST contain two clearly separated sections:

```
═══ 📋 FOR ADMIN ═══

[Summary information for the human relay]
- Skill name and description
- File size
- Expiry time

👉 Forward this entire message to the target agent.

═══ 🦞 FOR TARGET AGENT ═══

[Machine-readable instructions for the receiving agent]
- Download command (curl)
- Decrypt command (openssl)
- Extract command (tar/mv)
- Read command (cat)
- Cleanup command (rm)
```

### 9.2 Requirements

- The Note MUST be self-contained (no external references needed except the download URL)
- The Note MUST include the decryption password
- The Note MUST include the expiry warning
- The Note MUST use only standard shell commands available on all Unix-like systems
- The human relay SHOULD be able to forward the entire Note without modification

---

## 10. Identity and Certification (v3.0 planned)

### 10.1 Overview

The registry provides identity and certification services. Skills registered on c2cprotocol.org carry verifiable metadata about their origin and review status.

### 10.2 Identity

An **owner** is a person or organization that controls a skill's distribution. Owners are identified by:
- Linked accounts (e.g., GitHub, verified email)
- Organization membership

### 10.3 Certification Levels

| Level | Meaning |
|-------|---------|
| **self-signed** | Owner published, no external review |
| **reviewed** | Another trusted party has reviewed the skill |
| **org-certified** | An organization has officially certified the skill |

### 10.4 Verification

Receivers can verify a skill's certification by querying the registry:
```
GET /v1/skills/{name}@{version}/certification
```

---

## 11. Consent and Governance (v4.0 planned)

### 11.1 Consent Model

Transfer of a skill requires explicit consent from the owner. Consent can be:

| Type | Description |
|------|-------------|
| **Manual** | Owner reviews and approves each request individually |
| **Policy-based** | Owner defines rules (e.g., "auto-approve for members of org X") |
| **Open** | Owner marks the skill as freely available (equivalent to public) |

### 11.2 Organization Governance

Organizations can define policies that apply to all skills owned by their members:

- **Internal sharing**: All agents within the org can learn any org skill
- **Partner access**: Specific external orgs are pre-approved
- **Audit trail**: All transfers are logged

---

## 12. Security Model

### 12.1 Threat Model

C2C assumes:
- The communication channel between agents is **untrusted** (any IM platform)
- The temporary storage is **untrusted** (public file hosting)
- The human relay is **trusted** (the admin controls what gets forwarded)
- The registry (v3.0) is **trusted for metadata**, untrusted for storage (skills never stored on registry)

### 12.2 Security Properties

| Property | Mechanism |
|---|---|
| **Confidentiality** | AES-256-CBC encryption; only the password holder can decrypt |
| **Integrity** | OpenSSL verifies decryption; corrupted files fail to decrypt |
| **Availability** | 24h expiry limits exposure window |
| **Authorization** | Human relay (v1.x) or registry consent (v3.0+) |
| **Non-replayability** | One-time passwords; expired storage links |
| **Provenance** | Certification chain via registry (v3.0+) |

### 12.3 Human-in-the-Loop Guarantee

In v1.x, the protocol's security relies on the human relay:

1. The sender agent cannot send directly to the receiver — it presents a Note to its human
2. The human reviews the Note before forwarding
3. The human chooses the recipient
4. The human can refuse to forward — the ultimate kill switch

In v3.0+, human oversight shifts from relay to governance — owners set policies, approve requests, and audit transfers.

### 12.4 Shell Command Safety

| Command | Purpose | Safety |
|---|---|---|
| `curl` | Download/upload encrypted files | Files are encrypted before upload |
| `openssl` | Encrypt/decrypt with AES-256 | Industry-standard cryptography |
| `tar` | Pack/unpack skill directories | Operates only on skill files |
| `rm -f` | Clean up temp files in `/tmp/` | Only deletes files created by the protocol |

---

## 13. Transport Requirements

### 13.1 Minimum Requirements (v1.x human relay)

A C2C-compatible transport channel MUST support:
- Text messages of at least 2,000 characters
- Copy-paste or message forwarding

### 13.2 Compatible Transports

| Transport | Method |
|---|---|
| Telegram | Forward message |
| WeChat | Copy-paste or forward |
| Discord | Copy-paste |
| Signal | Forward message |
| WhatsApp | Forward message |
| Email | Forward email |
| SMS | Copy-paste (if within size limit) |
| c2cprotocol.org API (planned) | HTTPS |

---

## 14. Implementation Requirements

### 14.1 Sender Requirements

A conforming sender MUST:
1. Package the skill as `.tar.gz` (directory) or raw file (single file)
2. Generate a random password of at least 24 characters
3. Encrypt the package with AES-256-CBC + PBKDF2
4. Upload the encrypted package to temporary storage
5. Generate a Note following the format in Section 9
6. Present the Note to the human relay (v1.x) or submit to registry (v3.0+)

A conforming sender SHOULD:
- Include a `bottle.json` manifest (v2.0+)

### 14.2 Receiver Requirements

A conforming receiver MUST:
1. Parse the Note to extract URL, password, and file type
2. Download the encrypted package
3. Decrypt the package using the provided password
4. Extract/read the skill contents
5. Clean up temporary files

A conforming receiver SHOULD:
- Verify certification against the registry (v3.0+)
- Select the appropriate representation from `bottle.json` for its framework (v2.0+)

### 14.3 Dependencies (v1.x)

A conforming implementation MUST use only:
- `openssl` (encryption/decryption)
- `curl` (upload/download)
- `tar` (packaging, for directory skills)
- Standard shell utilities (`mkdir`, `rm`, `cat`)

No additional libraries, SDKs, or runtimes are required.

---

## 15. Versioning

This specification follows [Semantic Versioning](https://semver.org/):
- **Major**: Breaking changes to the protocol
- **Minor**: Backward-compatible additions
- **Patch**: Clarifications and editorial changes

Current version: **1.1.0**

### Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-18 | Initial specification — peer-to-peer transfer |
| 1.1.0 | 2026-03-18 | Protocol stack, Knowledge Bottle v2.0 schema, registry-mediated flow, identity/certification, consent/governance, cross-framework positioning |

### Roadmap

| Version | Focus |
|---------|-------|
| v2.0 | `bottle.json` manifest spec finalized; cross-framework importers |
| v3.0 | Registry API, identity, certification — c2cprotocol.org launch |
| v4.0 | Consent protocol, organizational governance |

---

## 16. References

- [Lobster Distill](https://github.com/c2c-protocol/lobster-distill) — Reference implementation for OpenClaw
- [c2cprotocol.org](https://c2cprotocol.org) — C2C Protocol official site (coming soon)
- [A2A Protocol](https://github.com/a2aproject/A2A) — Google's Agent-to-Agent communication protocol
- [Model Context Protocol](https://github.com/modelcontextprotocol) — Anthropic's tool integration protocol
- [Agent Client Protocol](https://agentclientprotocol.com/) — IDE-agent communication protocol
- [OpenClaw](https://github.com/openclaw/openclaw) — AI agent runtime

---

## License

This specification is released under the [MIT License](LICENSE).

---

*Claw-to-Claw: Distill knowledge, encrypt it, deliver in a bottle.* 🦞🧪
