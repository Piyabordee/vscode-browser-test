# Security policy

## Supported versions

This project is documentation-only — a single Markdown file with YAML front matter. There is **no executable code, no dependencies, and no network surface** shipped from this repository. As a result, the practical attack surface is limited to:

| Version | Supported |
|---------|-----------|
| `1.0.x` | ✅ Yes |
| `< 1.0` | ❌ No — please upgrade |

## Reporting a vulnerability

**Please do not open a public GitHub issue for security problems.**

Instead, report privately via one of the following channels:

1. **GitHub Security Advisories** (preferred):
   <https://github.com/<owner>/vscode-browser-test/security/advisories/new>
2. **Email:** open a draft issue asking for the security contact, or check the repository's commit history for a maintainer email.

Please include:

- A clear description of the issue
- Steps to reproduce (if applicable)
- The impact you believe it has (e.g. prompt injection, XSS in a rendered preview, etc.)
- A suggested fix, if you have one

## Response timeline

- **Initial acknowledgement:** within 5 business days
- **Triage and severity assessment:** within 10 business days
- **Fix or disclosure timeline:** negotiated case-by-case, target ≤ 30 days for confirmed issues

## What counts as a security issue here

Because the repository is docs-only, the realistic concerns are narrow:

| Concern | Real? |
|---------|-------|
| Prompt injection via the skill description (causing Claude to over-fire or under-fire) | **Yes** — high priority |
| Markdown rendering issues (XSS, link manipulation) on github.com | Low — github.com sanitizes Markdown, but flag anything suspicious |
| A malicious update to a transitive tool dependency | **Not applicable** — there are no dependencies |
| Secrets, tokens, or PII accidentally committed | **Yes** — high priority |
| Backdoored code in the skill | **Not applicable** — no code is shipped |

If you are unsure whether something is a security issue, report it anyway — we would rather triage a non-issue than miss a real one.
