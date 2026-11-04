# AGENTS.md — Awesome Islam

Read `SOP.md` first — it is the authoritative maintenance guide for this repo.

## Non-negotiables
- Treat this as a **public** repo: never commit secrets, tokens, or credentials.
- No push/merge/settings changes without explicit owner approval.
- One resource per PR; every link must pass SOP §2 vetting (HTTPS, resolves, legitimacy, safety, quality).

## Quick commands
```bash
gh auth status
git status --short
GITHUB_TOKEN=$(gh auth token) npx --yes awesome-lint README.md   # needs token for topic check
git grep -n -E 'AKIA[0-9A-Z]{16}|ghp_[A-Za-z0-9]{20,}|github_pat_[A-Za-z0-9_]{20,}|sk-[A-Za-z0-9_-]{20,}|-----BEGIN [A-Z ]*PRIVATE KEY-----' $(git rev-list --all) -- . || echo 'no secret patterns found'
```

## Repo facts (2026-11-04)
- Desktop project: **awesome-islam** at `/home/ayham/projects/awesome-islam`.
- `gh` auth: **JobeEnterprise**, ADMIN. Branch protection on `main` requires 1 review + CODEOWNERS — use `gh pr merge --admin` for owner PRs.
- Security enabled: secret scanning + push protection, private vulnerability reporting.
- Topics include `awesome` + `awesome-list` (required by awesome-lint).
