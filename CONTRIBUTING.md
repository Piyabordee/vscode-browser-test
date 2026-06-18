# Contributing to vscode-browser-test

Thanks for your interest in improving this skill! The skill is intentionally small and focused, so most contributions are improvements to the documentation, examples, or tooling around the single `SKILL.md` file.

---

## Code of conduct

This project follows the [Contributor Covenant](https://www.contributor-covenant.org/version/2/1/code_of_conduct/). By participating, you agree to abide by its terms.

---

## How to file an issue

- **Bug report** → use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.md)
- **Feature request** → use the [feature request template](.github/ISSUE_TEMPLATE/feature_request.md)
- **Security issue** → **do not** open a public issue; follow [`SECURITY.md`](SECURITY.md) instead

---

## Development setup

The "code" in this repository is a single Markdown file with a YAML front matter. There is **nothing to install** — just edit `.claude/skills/vscode-browser-test/SKILL.md` in any editor and review the rendered result.

### Recommended local tooling

| Tool | Why |
|------|-----|
| VS Code | Live-preview Markdown and validate YAML front matter |
| [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint) | Catches style issues in `.md` files |
| [Vale.sh](https://vale.sh/) (optional) | Prose linter for the README and skill body |

---

## Pull request workflow

1. **Open an issue first** for anything beyond a typo or trivial fix. This lets us agree on the approach before you invest time.
2. **Fork the repo** and create a branch from `main`:
   ```bash
   git checkout -b fix/typo-in-tools-table
   # or
   git checkout -b docs/add-troubleshooting-entry
   ```
3. **Edit** the relevant `.md` file(s).
4. **Lint** — if you have `markdownlint` or `Vale` installed, run them locally.
5. **Open a PR** using the [PR template](.github/pull_request_template.md). Link to the issue it closes (`Closes #123`).
6. **Wait for review.** A maintainer will review within a few business days. Be prepared to iterate.

---

## Style guide for the skill body

When editing `.claude/skills/vscode-browser-test/SKILL.md`, follow these rules — they are what make the skill portable across projects and contributors.

### 1. Stay project-agnostic

- ❌ `file:///c:/dev/my-app/docs/mockup/dashboard.html`
- ✅ `file:///absolute/path/to/docs/mockup/dashboard.html`
- ❌ `c:/dev/e-ticket/...`
- ✅ `<absolute-path-to>/...`
- ❌ hard-coded UI labels in any non-English language
- ✅ `use readPage to enumerate the actual labels`

The skill must work for **any** user who copies the file. If your change only helps one project, it belongs in that project's docs, not here.

### 2. Mind the description in the YAML front matter

The `description` field is what tells Claude *when* to load the skill. Keep it:

- **Specific** — it should fire only on browser-verification tasks, not on every "fix this" prompt.
- **Action-oriented** — start with verbs and concrete trigger phrases the user is likely to say.
- **Concise** — aim for one or two sentences, plus a comma-separated list of trigger phrases in quotes.

### 3. Verify tool-behavior claims

If you add a tip that depends on how a specific tool behaves (e.g. "`runPlaywrightCode` can call `setInputFiles`"), link to the [upstream VS Code docs](https://code.visualstudio.com/docs/copilot/guides/browser-agent-testing-guide) and check that the behavior is still accurate. The browser-agent API is **experimental** and may change.

### 4. Prefer concrete examples over prose

The skill is loaded into the agent's context and then mostly ignored — what gets read in practice is the tables, code blocks, and bullet lists. Keep dense paragraphs to a minimum.

### 5. Run the agent against your change

Before opening a PR, copy your edited `SKILL.md` into a real project, start VS Code, open Claude chat, and try the trigger phrases you mention. Confirm the skill is loaded (Claude will say so) and that the agent follows the workflow you described.

---

## Commit message convention

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
docs: fix typo in tools table
feat: add responsive-check recipe
fix: clarify session-isolation rules
chore: bump version to 1.0.0
```

---

## Release process

1. Update the version in `CHANGELOG.md` and move entries from `[Unreleased]` to a dated `[X.Y.Z]` block.
2. Commit with `chore: release X.Y.Z`.
3. Tag the commit: `git tag vX.Y.Z && git push --tags`.
4. GitHub Releases are auto-generated from tags.

---

## Questions?

Open a discussion in the issue tracker (use the **Question** label) or reach out to the maintainers via the contact in [`SECURITY.md`](SECURITY.md).
