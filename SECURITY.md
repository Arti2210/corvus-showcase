# Security Policy

## Reporting a vulnerability

**This public repository contains documentation and screenshots only — no executable code.**

Even so, if you find a problem with anything published here, please report it:

- **Preferred:** message the owner directly on Telegram — see the contact link in [README.md](README.md).
- Please include: what you found, where (file / section), and how to reproduce it.
- Please **do not** open a public GitHub issue for anything security-sensitive.
- You can expect an acknowledgement. Fixes depend on severity and on whether the affected part of
  the product is actually deployed somewhere.

## Is there source code here?

No. The repository is a showcase: Markdown documentation, screenshots from a **de-identified demo
build with mocked data**, a license and this policy. There are no credentials, no runtime data, no
databases and no session files in this repository.

If you believe source code was published here by mistake, report it immediately via the channel above
— treat it as an incident.

## How this repository was checked

Before publication, the repository content was scanned for:

- API tokens and key patterns (bearer tokens, cloud/Telegram/LLM key prefixes, JWT-shaped strings);
- environment files (`.env` in any form) and any embedded credentials;
- Telegram session files (`*.session`) and auth state;
- databases, backups and archives (`.db`, `.sqlite*`, `.sql`, `.tar.gz`, `.zip`);
- personal data: real phone numbers, email addresses, personal names, Telegram user IDs;
- internal domains, IP addresses, hostnames and internal URLs;
- private key material (`BEGIN ... PRIVATE KEY` blocks).

The scan result is recorded in [SECRET_SCAN.md](SECRET_SCAN.md). Screenshots come from a demo build
that runs entirely on mocked data and does not touch any real account.

## Security properties of the product (for reference)

These are claims about the private product, sourced from the internal capability audit — not from
this repository:

- Sensitive database fields are encrypted with Fernet; decryption failure raises an error instead of
  silently writing plaintext.
- Encryption keys can be rotated without losing history.
- PII is masked before content is sent to an LLM.
- Prompt-injection guards are applied at two independent points, with fuzz coverage.
- Access control: owner-only routes, authenticated API and WebSocket endpoints.

## Scope

This policy covers **this repository only**. The product itself is not open source and has no
public issue tracker. Vulnerability reports about a deployed instance go to the operator of that
instance, not to this repository.