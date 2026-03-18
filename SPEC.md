# Claw-to-Claw (C2C) Protocol Specification v1.0

**Status:** Draft
**Date:** 2026-03-18
**Authors:** Claw-to-Claw Protocol Contributors

---

## 1. Overview

The Claw-to-Claw (C2C) protocol defines a standard method for transferring skills (executable knowledge) between AI agents through human relay. It is designed to work across any text-capable communication channel without requiring direct network connectivity between agents.

### 1.1 Design Goals

- **Transport-agnostic**: Works on any channel that can carry text (IM, email, voice, paper)
- **Human-in-the-loop**: Humans control all transfers, maintaining oversight and trust
- **Privacy-first**: End-to-end encryption with one-time passwords
- **Zero dependencies**: Uses only ubiquitous system tools
- **Simplicity**: Sending requires one command; receiving requires five lines

### 1.2 Non-Goals

- Real-time agent-to-agent communication (see [A2A](https://github.com/a2aproject/A2A))
- Tool discovery and invocation (see [MCP](https://github.com/modelcontextprotocol))
- Persistent agent-to-agent connections
- Automated transfers without human involvement

---

## 2. Core Concept — Knowledge Bottle

The C2C protocol revolves around two core mechanisms: **Knowledge Distillation** and **Human Relay**. The reference implementation is called **Lobster Distill**.

### 2.1 Step 1: Skill Distillation

The source AI (e.g., an agent that can manage calendars) **distills** the internal knowledge required to perform a task — including prompts, tool-calling logic, parameter constraints, etc. — into a compact, structured data package called a **Knowledge Bottle**.

### 2.2 Step 2: Encrypt & Package

To prevent tampering or leakage during transfer, the Knowledge Bottle is encrypted using AES-256-CBC + PBKDF2 with a one-time password. Only the intended recipient with the correct key can open it. The result is a plain-text-safe artifact (encrypted binary uploaded to temporary storage).

### 2.3 Step 3: Human Relay

This is what makes C2C unique — transfer does **not** depend on APIs or direct network connections. Instead, it leverages any text channel a human can use:

- Paste the Note into WeChat, Telegram, email, or any messenger
- The recipient copies the Note and pastes it into the target AI's chat
- The human acts as a **physical courier**, bypassing all network restrictions

### 2.4 Step 4: Target AI Load & Execute

The target AI receives the Note and executes through the C2C client (e.g., Lobster Distill's `receive.sh`):

1. Download the encrypted Knowledge Bottle using the provided URL
2. Decrypt using the one-time password
3. Extract and parse the skill description and invocation methods
4. Dynamically load the skill — the target AI **instantly gains the same capability** as the source AI

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

> **Key insight**: The entire process is **text-based**. No network API or persistent connection is required between agents. All sensitive content is encrypted and auto-expires within 24 hours.

---

## 3. Terminology

| Term | Definition |
|---|---|
| **Agent** | An AI system capable of executing skills (e.g., an OpenClaw instance) |
| **Skill** | A transferable unit of executable knowledge (code, configuration, documentation) |
| **Sender** | The agent that packages and encrypts a skill for transfer |
| **Receiver** | The agent that downloads, decrypts, and installs a skill |
| **Relay** | The human who forwards the Note from sender to receiver |
| **Package** | The encrypted archive containing the skill |
| **Note** | The text message containing download instructions and credentials |
| **Knowledge Bottle** | The distilled, encrypted package of an AI skill — the core transfer artifact in C2C |
| **Temporary Storage** | A file hosting service used to store the encrypted package |

---

## 4. Protocol Flow

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

## 5. Package Format

### 5.1 Skill Packaging

A skill MUST be packaged as one of:

| Type | Format | Use Case |
|---|---|---|
| **Directory** | `.tar.gz` | Multi-file skills (code + docs + config) |
| **Single file** | Raw file | Single-file skills or documents |

For directory packaging:
```bash
tar czf <name>.tar.gz -C <skill-directory> .
```

### 5.2 Encryption

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

### 5.3 Password Generation

Passwords MUST be:
- At least 24 characters long
- Randomly generated using a cryptographically secure source
- Single-use (one password per transfer)

Reference implementation:
```bash
openssl rand -base64 24
```

---

## 6. Temporary Storage

### 6.1 Requirements

The temporary storage service MUST:
- Accept file uploads via HTTP
- Return a publicly accessible download URL
- Automatically delete files after a defined period
- Not require authentication for download

### 6.2 Recommended Services

| Service | Max Size | Expiry | Upload Method |
|---|---|---|---|
| litterbox.catbox.moe | 1 GB | 24h | `multipart/form-data` |

### 6.3 Expiry

Files SHOULD have a maximum lifetime of **24 hours**. The Note MUST inform the receiver of the expiry window.

---

## 7. Note Format

The Note is the core transfer artifact. It MUST be plain text, readable by both humans and AI agents.

### 7.1 Structure

A Note MUST contain two clearly separated sections:

```
═══ 📋 FOR ADMIN / 管理员请读 ═══

[Summary information for the human relay]
- Skill name and description
- File size
- Expiry time

👉 Forward this entire message to the target agent.

═══ 🦞 FOR TARGET AGENT / 目标龙虾请读 ═══

[Machine-readable instructions for the receiving agent]
- Download command (curl)
- Decrypt command (openssl)
- Extract command (tar/mv)
- Read command (cat)
- Cleanup command (rm)
```

### 7.2 Requirements

- The Note MUST be self-contained (no external references needed except the download URL)
- The Note MUST include the decryption password
- The Note MUST include the expiry warning
- The Note MUST use only standard shell commands available on all Unix-like systems
- The human relay SHOULD be able to forward the entire Note without modification

### 7.3 Section Markers

The two sections MUST be clearly delimited so that:
- The human relay knows which part is for them (summary/instructions)
- The receiving agent knows which part to execute (download/install commands)

Recommended markers:
```
═══ 📋 FOR ADMIN ═══
═══ 🦞 FOR TARGET AGENT ═══
```

---

## 8. Security Model

### 8.1 Threat Model

C2C assumes:
- The communication channel between agents is **untrusted** (any IM platform)
- The temporary storage is **untrusted** (public file hosting)
- The human relay is **trusted** (the admin controls what gets forwarded)

### 8.2 Security Properties

| Property | Mechanism |
|---|---|
| **Confidentiality** | AES-256-CBC encryption; only the password holder can decrypt |
| **Integrity** | OpenSSL verifies decryption; corrupted files fail to decrypt |
| **Availability** | 24h expiry limits exposure window |
| **Authorization** | Human relay decides what to forward and to whom |
| **Non-replayability** | One-time passwords; expired storage links |

### 8.3 Human-in-the-Loop Guarantee

The protocol's security fundamentally relies on the human relay:

1. **The sender agent cannot send directly to the receiver** — it can only present a Note to its human
2. **The human reviews the Note** before forwarding
3. **The human chooses the recipient** — the sender has no control over where the Note goes
4. **The human can refuse to forward** — this is the ultimate kill switch

This is a feature, not a limitation. It provides an irreducible layer of human oversight that cannot be bypassed by either agent.

### 8.4 Shell Command Safety

C2C implementations use shell commands that security scanners may flag:

| Command | Purpose | Safety |
|---|---|---|
| `curl` | Download/upload encrypted files | Files are encrypted before upload |
| `openssl` | Encrypt/decrypt with AES-256 | Industry-standard cryptography |
| `tar` | Pack/unpack skill directories | Operates only on skill files |
| `rm -f` | Clean up temp files in `/tmp/` | Only deletes files created by the protocol |

---

## 9. Transport Requirements

### 9.1 Minimum Requirements

A C2C-compatible transport channel MUST support:
- Text messages of at least 2,000 characters
- Copy-paste or message forwarding

### 9.2 Compatible Transports

Any text-capable channel works:

| Transport | Method |
|---|---|
| Telegram | Forward message |
| WeChat | Copy-paste or forward |
| Discord | Copy-paste |
| Signal | Forward message |
| WhatsApp | Forward message |
| Email | Forward email |
| SMS | Copy-paste (if within size limit) |
| Voice call | Read aloud (extreme case) |
| Paper | Print and hand over (extreme case) |

---

## 10. Implementation Requirements

### 10.1 Sender Requirements

A conforming sender MUST:
1. Package the skill as `.tar.gz` (directory) or raw file (single file)
2. Generate a random password of at least 24 characters
3. Encrypt the package with AES-256-CBC + PBKDF2
4. Upload the encrypted package to temporary storage
5. Generate a Note following the format in Section 6
6. Present the Note to the human relay

### 10.2 Receiver Requirements

A conforming receiver MUST:
1. Parse the Note to extract URL, password, and file type
2. Download the encrypted package
3. Decrypt the package using the provided password
4. Extract/read the skill contents
5. Clean up temporary files

### 10.3 Dependencies

A conforming implementation MUST use only:
- `openssl` (encryption/decryption)
- `curl` (upload/download)
- `tar` (packaging, for directory skills)
- Standard shell utilities (`mkdir`, `rm`, `cat`)

No additional libraries, SDKs, or runtimes are required.

---

## 11. Versioning

This specification follows [Semantic Versioning](https://semver.org/):
- **Major**: Breaking changes to the protocol
- **Minor**: Backward-compatible additions
- **Patch**: Clarifications and editorial changes

Current version: **1.0.0**

---

## 12. References

- [Lobster Distill](https://github.com/c2c-protocol/lobster-distill) — Reference implementation for OpenClaw
- [A2A Protocol](https://github.com/a2aproject/A2A) — Google's Agent-to-Agent communication protocol
- [Model Context Protocol](https://github.com/modelcontextprotocol) — Anthropic's tool integration protocol
- [OpenClaw](https://github.com/openclaw/openclaw) — AI agent runtime

---

## License

This specification is released under the [MIT License](LICENSE).

---

*Claw-to-Claw: Distill knowledge, encrypt it, deliver in a bottle.* 🦞🧪
