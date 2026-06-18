# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed
- Generalized all examples to be framework-agnostic (Next.js, Vite, Astro, plain HTML, etc.)
- Removed project-specific paths and Thai-language UI labels from the skill body
- Replaced internal wiki-style `[[docs/...]]` cross-references with public URLs
- Replaced project-specific "Common scenarios in this project" section with generic recipes

### Added
- `README.md` with installation, prerequisites, and usage examples
- `LICENSE` (MIT)
- `CONTRIBUTING.md`
- `SECURITY.md`
- `CHANGELOG.md` (this file)
- `.gitignore` and `.editorconfig`
- GitHub issue and pull-request templates

### Removed
- Duplicate `SKILL.md` at the repository root (canonical copy lives at `.claude/skills/vscode-browser-test/SKILL.md`)

## [1.0.0] — 2025-XX-XX

### Added
- Initial release of the `vscode-browser-test` Claude skill
- Coverage of all 10 native VS Code browser tools
- Build → test → debug workflow
- Anti-patterns and troubleshooting sections
- Quick-reference table for common user phrases
