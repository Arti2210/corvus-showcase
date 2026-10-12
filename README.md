<div align="center">

# 🦅 Corvus

### A personal AI assistant that runs on your own server, talks to people through Telegram, and remembers who it talks to.

[Українська версія](README.uk.md) · [Capabilities](#-capabilities) · [Architecture](#-architecture) · [Status](#-status) · [Contact](#-contact)

</div>

---

## What is this?

**Corvus** is a single-user AI assistant that runs on one VPS and communicates with people through
Telegram. It is not a wrapper around someone else's chatbot — it is a **modular monolith with
hexagonal architecture**, where the business logic never knows which messenger it is talking to.

It can operate in **two modes**: as an ordinary bot, or by logging into **your own Telegram account**
so it writes on your behalf. It builds long-term memory of every contact (facts, episodes,
relationships, a knowledge graph), learns your writing style, handles voice messages and live voice
calls, and manages tasks and reminders.

> ⚠️ **This repository is a showcase, not the source code.** The source is private and proprietary.
> This repository contains documentation and screenshots only. See [LICENSE](LICENSE).

---

## ✨ Key capabilities

### 🕸️ Memory that is actually structured
An hourly background job consolidates conversations into **knowledge atoms, episodes, entities and
relations**, stored in a queryable knowledge graph. Every contact gets their own profile — facts,
traits, relationship score. Three-layer RAG (conversation history + vector store + extracted facts)
means answers are grounded in what really happened, not invented.

*Source: `CORVUS_CAPABILITIES.md` §1.1*

### 🎭 Two ways to be present in Telegram
Ordinary bot mode, **or** userbot mode where the assistant enters your account and replies as you —
with human-like pauses, typing simulation and message splitting, and an on-disk session encrypted
with Fernet. Also: live voice calls with **barge-in** (the assistant can be interrupted) driven by
voice-activity detection.

*Source: `CORVUS_CAPABILITIES.md` §1.2*

### 🔗 A resilient LLM chain with a local fallback
**Four-level fallback**: primary provider → secondary provider → OpenRouter → local Ollama.
Plus a token budget enforced on every call, a think-step planner, a self-critic, and a semantic cache
so repeated questions cost less.

*Source: `CORVUS_CAPABILITIES.md` §1.3*

### 🔒 Security that fails closed
All sensitive database fields are encrypted with Fernet, and decryption failure **raises an error
rather than silently writing plaintext**. Keys can be rotated without losing history. PII is masked
before anything is sent to an LLM. Prompt-injection guards sit at **two independent points**, with
60 fuzz vectors and a regression suite that ensures ordinary two-language conversation is not
blocked. Rate limits: 30 text / 10 voice / 5 document requests per minute.

*Source: `CORVUS_CAPABILITIES.md` §1.8*

### 📱 Control surface: REST + MiniApp
About **90 REST endpoints** plus a Telegram MiniApp on React 18 + Vite + TypeScript (9 pages:
Agent, Contacts, Dialogs, Memory, Roles, Tasks, System, Dashboard). Auth is based on Telegram
`initData` HMAC verification and owner-only routes. Background work is visible through
**Prometheus metrics** and WebSocket streaming of agent activity.

*Source: `CORVUS_CAPABILITIES.md` §1.6, §1.9*

---

## 🏗 Architecture, in two words

**Hexagonal modular monolith.** `domain` and `application` in the middle, `adapters` around the
edges, everything wired through a single dependency-injection container. One core domain; swapping
or adding a channel means writing one adapter, not rewriting the logic.

*Source: `CORVUS_CAPABILITIES.md` §0, `/root/Corvus-X/README.md`*

---

## 🧰 Stack

| Layer | Technologies |
|---|---|
| Language | Python 3.11 |
| Architecture | Hexagonal (domain / application / ports / adapters), DI container |
| Messaging | Telegram Bot API + Telethon userbot, WebSocket streaming |
| Frontend | React 18, Vite, TypeScript (Telegram MiniApp) |
| LLM | Multi-provider chain, semantic cache, token budget, local Ollama fallback |
| Voice | faster-whisper (local STT), ElevenLabs / OpenAI (TTS), VAD call engine |
| Storage | SQLite with field-level Fernet encryption, Redis, vector store |
| Ops | Prometheus metrics, OpenTelemetry + Langfuse, health checks, disk guard, backups |
| Quality | pytest, ruff, bandit, pip-audit, GitHub Actions CI |

---

## 📊 Verified numbers

Every figure below comes from the internal capability audit dated **2026-10-12**
(`CORVUS_CAPABILITIES.md`). Nothing here is estimated.

| Fact | Value | Source |
|---|---|---|
| Test suite | 2549 passed, 13 skipped (242 test files) | `CORVUS_CAPABILITIES.md` §0 |
| Modules in `src/` | ~250 | `CORVUS_CAPABILITIES.md` §0 |
| REST endpoints | ~90 | `CORVUS_CAPABILITIES.md` §1.6 |
| Background cycles | 22 | `CORVUS_CAPABILITIES.md` §0 |
| ReAct agent tools | 18 | `CORVUS_CAPABILITIES.md` §1.7 |
| LLM fallback levels | 4 | `CORVUS_CAPABILITIES.md` §1.3 |
| MiniApp pages | 9 | `CORVUS_CAPABILITIES.md` §1.6 |
| Internal version | 0.5.0 | `CORVUS_CAPABILITIES.md` §0 |

---

## 🚫 What this is **not**

Stated plainly, because it matters more than any feature list:

- **Single-user, not multi-tenant.** One owner, one assistant. There is no billing, no subscription
  system and no tenant isolation — those modules do not exist.
- **Telegram only.** No Instagram, Signal or WhatsApp adapters exist (an older internal doc that
  mentions them is outdated).
- **No fully offline mode is verified.** Local models exist as a fallback (faster-whisper, Ollama),
  but routing all traffic through them is neither documented nor tested.
- **YouTube Music integration is not wired into the runtime** — the adapter exists but is unused.
- **Proactive first-contact messaging is intentionally disabled** by owner decision.
- **No load-tested production track record with real accounts.** The test suite is green; end-to-end
  load validation under real accounts has not been performed.

*Source: `CORVUS_CAPABILITIES.md` §3*

---

## 📸 Screenshots

All screenshots come from a **de-identified demo build** with mocked data — no real contacts, no
real conversations.

| | |
|---|---|
| ![Dashboard](screenshots/01-dashboard.png) | ![Contacts](screenshots/02-contacts.png) |
| Dashboard | Contacts with scores and roles |
| ![Logs](screenshots/03-logs.png) | ![Roles](screenshots/04-roles.png) |
| Logs | Roles |
| ![Dialogs](screenshots/05-dialogs.png) | ![Agent](screenshots/06-agent.png) |
| Dialogs | ReAct agent |
| ![Knowledge graph](screenshots/07-knowledge-graph-3d.png) | |
| 3D knowledge graph | |

---

## 📌 Status

- **Source code: private, proprietary.** Not published, not open source.
- **This repository: public showcase** — documentation and screenshots.
- **Live demo: on request** via Telegram, no installation required.
- **Deployment / integration: on request** — delivered as a private handover or a private repository.

The demo build you can be shown runs entirely on mocked data and does not touch any real account.

---

## 📬 Contact

Telegram: **[https://github.com/Arti2210](https://github.com/Arti2210)**

> ⚠️ `https://github.com/Arti2210` is a placeholder — replace it with the real contact link before publishing
> (see [PUBLISH_INSTRUCTIONS.md](PUBLISH_INSTRUCTIONS.md)).

---

## 📄 License

Proprietary. The code is **not** open source and **not** licensed under any OSI-approved license.
See [LICENSE](LICENSE) and [SECURITY.md](SECURITY.md).