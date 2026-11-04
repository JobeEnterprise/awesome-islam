# SOP — Awesome Islam maintenance (v2)

Repo: `JobeEnterprise/awesome-islam` — curated list of Islamic resources (Arabic/English).
Local project: `/home/ayham/projects/awesome-islam` (Desktop project: **awesome-islam**).
Any AI operating this repo must follow this file. Treat the repo as **public**: assume every committed byte is world-readable forever.
Research basis (2026-11-04): GitHub security docs, awesome-list conventions, link-vetting methodology. Sources are linked where the rule depends on them.

## 0. Preflight (every session, 2 min)

```bash
cd /home/ayham/projects/awesome-islam
git status --short
git fetch origin
gh auth status        # confirmed: JobeEnterprise, ADMIN, scopes repo+workflow
```

- To **edit/push** you need **write access** to `JobeEnterprise/awesome-islam` (current: `gh` logged in as **JobeEnterprise**, `viewerPermission: ADMIN`). Never paste tokens in chat.
- If write auth is missing, do read-only work (clone, audit, draft) and stop before push/PR.
- Push protection can block example secret-like strings — keep false-positive allowlists in `.gitleaks.toml` / GitHub bypass rules, never by disabling scanning.

## 0.1 Repository security settings (owner approval required)

Verify once, then quarterly. Settings changes need explicit owner approval.

- [ ] **Secret Protection + push protection** ON (Settings → Security and quality → Advanced Security). Clarify the defaults: secret-scanning *alerts* for public repos are on by default, and user-level push protection is on by default — but **repository-level push protection is off by default** and must be enabled. Live check (2026-11-04): `secret_scanning`, `secret_scanning_push_protection`, and validity checks are currently **disabled** on this repo — enable them. <https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-security-and-analysis-settings-for-your-repository>
- [ ] **Private vulnerability reporting** ON + `SECURITY.md` published. <https://docs.github.com/en/code-security/how-tos/report-and-fix-vulnerabilities/configure-vulnerability-reporting/configure-for-a-repository>
- [ ] **Fork approval**: require approval for all external contributors; default `GITHUB_TOKEN` read-only. Set Settings → Actions → General → workflow permissions to read-only.
- [ ] **Branch protection on `main`** (or a ruleset): currently **not protected** (live check 2026-11-04: API returns "Branch not protected"; no rulesets). Owner approval required to enable. Verify with `gh api repos/JobeEnterprise/awesome-islam/branches/main/protection` and `gh api repos/JobeEnterprise/awesome-islam/rulesets`.
- [ ] **Dependabot alerts / CodeQL**: only if dependency manifests or code exist. This is a Markdown-only list — skip CodeQL unless workflows/code are added.

## 1. Secrets — non-negotiable

- Never commit: `.env`, API keys, tokens, passwords, private keys, `*.pem`, credentials files. `.gitignore` blocks these at the root.
- Before any push, run **both** scans and require **0 hits**:
  1. `gitleaks detect --source . --verbose` (pre-commit hook locally — it scans git history directly; in CI, `actions/checkout` needs `fetch-depth: 0` or the history scan misses old leaks).
  2. Fallback grep (tree + history):
     ```bash
     git grep -n -E 'AKIA[0-9A-Z]{16}|ghp_[A-Za-z0-9]{20,}|github_pat_[A-Za-z0-9_]{20,}|sk-[A-Za-z0-9_-]{20,}|-----BEGIN [A-Z ]*PRIVATE KEY-----' $(git rev-list --all) -- . || echo 'no secret patterns found'
     ```
- Report scan results by file/line/pattern only — never print secret values.
- If a secret is ever committed: **stop, rotate/revoke it immediately**, then purge history (`git filter-repo`/BFG) only with explicit owner approval.

## 2. Link vetting (present + future)

Every link must pass all checks before it is added or kept. Automated checks (lychee, HTTP audit) cover resolvability only — **manual vetting is still required** (§2.1–§2.3).

1. **Resolves**: HTTP 200 after redirects; final URL is the resource itself, not a homepage/soft-404.
2. **Legit**: domain owner matches the resource (official site, scholar, publisher, university, or established platform). No impersonation, no lookalike domains, no URL shorteners.
3. **Safe**: HTTPS only; no executables/archives (`exe/msi/dmg/apk/bat/sh/zip/...`); no deceptive download buttons; no forced installs; no IP-literal URLs.
4. **Downloadable files** (PDF/EPUB/etc.): allowed only from trusted publishers/libraries; require manual review. Never download/run binaries on the host to "test" them.
5. **Virus-free**: an HTTP check cannot prove this. Use HTTPS + trusted domain + no suspicious payload as the bar, and say "no indicators found" — never claim a guarantee. Google Safe Browsing / VirusTotal / urlscan are **known-bad lists, not proof of safety**: they are strongest on already-known-bad URLs and weak on brand-new pages. Safety points in the score require a Safe Browsing check to have actually been run — no check, no points — and a clean result on a brand-new page is weak evidence, not proof.
6. **High quality**: accurate description, title/author match, no duplicate, correct category, neutral cataloging (describe, don't preach or polemicize). Prefer primary/authoritative sources (Qur'an, authentic Hadith collections, recognized scholars/institutions; verify authority via credentials, institutional affiliation, and publication history).
7. **Platform content** (YouTube/archive.org): trusted platform ≠ guaranteed content quality — check channel/playlist title and uploader (YouTube verification badge = official channel of a creator/brand, verified by YouTube), note channel vs playlist, and archive.org item metadata/uploader/collection.

Fail any check → do not add; if already listed → flag for removal in the audit report.

### 2.1 Link quality score (0–100)

Score every link in the audit CSV. Auto-fail (score 0, `review_remove`): HTTP 404/410, **non-HTTPS**, URL shortener, IP-literal host, executable/archive, impersonation/lookalike or homograph/IDN domain, redirect chain >2 hops or to an unrelated registrable domain, deceptive download, phishing/credential harvesting, paywall for a listed-as-free resource, hate/extremism, or copyright-infringing content.

| Criterion | Points | How to judge |
|---|---|---|
| Resolves, final URL is the resource | 20 | 200 + correct final URL; redirect chain ≤2 hops to same resource |
| HTTPS + safe transport | 10 | HTTPS everywhere; HTTP = review (max 5); mixed content = 0 |
| Legitimacy / authority | 30 | 25–30 official/publisher/scholar/institution; 15–24 established platform uploader / .org/.edu; 11–14 small but known source; 0–10 unknown domain |
| Safety signals | 20 | Safe Browsing clean + no deceptive elements = 20; file download caps at 10 until manual review |
| Quality / fit | 20 | Title+description accurate, correct category, non-duplicate. "Still maintained" = updated or active within the last 24 months, no dead-project/abandoned notice |

- **80–100** → `keep`. **60–79** → `keep_spotcheck` (human verifies content matches title). **1–59** → `review_manual`. **0 / auto-fail** → `review_remove`.
- Keep the score + evidence (final URL, host, title) in the audit CSV so decisions are reproducible.
- **Gates (anti-gaming)**: points alone can't force a `keep`.
  - `keep` additionally requires Legitimacy ≥ 15 **and** Safety ≥ 10. An unknown-domain link (Legitimacy ≤ 10) is capped at **79** (`keep_spotcheck`) no matter how high the points total.
  - HTTP-only (non-HTTPS) links are capped at **59** (`review_manual`) and score 0 on Safety — transport failure can't be compensated by other points.
  - File downloads are capped at **79** until manual review; after manual review passes, Safety may rise to 20.
  - Platform links (YouTube/archive.org) get Legitimacy from the **uploader/channel**, not the platform: verified/official uploader 20–24 (a YouTube badge proves identity, not content quality — cap at 24 without reviewing the content), established uploader 15–20, unknown uploader ≤ 14.

### 2.2 Automated link check (CI)

Run on every PR/push and fail the build on broken/redirected/non-HTTPS links. Scan **all** Markdown files, not just `README.md`:
```bash
lychee --no-progress --accept 200 --require-https "**/*.md"
```
Lychee automates §2.1 checks 1–3 only. A passing lychee run never replaces the manual legitimacy/quality review.

### 2.3 Malicious-link PR policy

One suggested resource per PR. No auto-merge, ever. Maintainer verifies domain, HTTPS, no executables, and score ≥ 60 before merge; close spam/deceptive PRs without engagement.

## 3. Cataloging & classification (awesome-list rules)

- Format: `- [Name](url) - One-line description ending with a period. \`Tag\``. Tags: `Book`, `PDF`, `YouTube Channel`, `Website`, `iOS`/`Android` when true.
- **Alphabetical order** within each section by resource name (maintainer convention — `awesome-lint` does not enforce sort order).
- **No duplicates**: dedupe by canonical URL (strip `www.`, trailing slash, tracking params) — same resource, different mirror = keep the canonical one.
- Sections: Quran, Tajweed, Tafsir, Arabic, Seerah, Hadith, General Resources. A resource may fit two sections — pick the best fit, don't duplicate.
- `Needs Categorization` must stay empty: every new item is categorized before merge.
- Descriptions must say **why it is worth including** (one clause), not just restate the title — bare links read as link farms.
- Keep the `## Contents` list and heading anchors in sync when adding/removing sections. Keep the README disclaimer; paraphrase copied descriptions.
- **Awesome compliance**: lowercase `contributing.md` linked from README; `## Contents` near the top; Awesome badge; `npx awesome-lint README.md` passes with **0 errors**. Current README does not yet pass (missing badge, contributing link, TOC/anchor sync, punctuation) — those fixes are pending and tracked as README work, not SOP work. License note: repo is currently MIT — CC0 is required for submission to `sindresorhus/awesome` (owner decision, not a silent change).

## 4. PR workflow & branch protection

- Target `main` only via PR. Branch: `docs/<short-description>`. Conventional Commits (`docs: ...`). One resource per PR. Each PR closes its issue (`Closes #N`) and includes the audit row for added links.
- **Branch protection on `main`** (owner approval to set): require PR, ≥1 approving review, dismiss stale reviews, require branches up-to-date, block force pushes/deletions, and enable **"Require review from Code Owners"** — CODEOWNERS only applies once it is on the base branch. Repo rulesets are an acceptable alternative.
- **Fork/Actions safety**: avoid `pull_request_target` unless specifically reviewed (it can run untrusted fork code with secrets = "pwn request"); prefer `pull_request` with least-privilege `permissions:`, `persist-credentials: false` on checkout, and pin third-party Actions to full commit SHAs verified against the action's own repo. No secrets in workflows; never check out untrusted PR code with credentials.
- Agent-made changes: draft branch + PR only. Never push to `main` directly. Never merge without owner approval.

## 5. Monitoring cadence

- **Weekly**: list open PRs/issues, flag new ones for review (no auto-merge). Triage backlog: currently PRs #10/#9/#8, issues #6/#7.
- **Monthly**: re-run full link audit + gitleaks history scan; update `docs/audits/link-audit-YYYY-MM-DD.csv`; summarize dead links, score changes, and removals.
- **After any HTTPS/domain change**: spot-check affected links within a day.

## 6. Audit artifacts & required files

- `docs/audits/link-audit-<date>.csv` — text, url, final_url, status, content_type, title, host, flags, **score**, recommendation, error. Future audits add per-criterion columns (`resolves_score`, `transport_score`, `legitimacy_score`, `safety_score`, `quality_score`) plus evidence columns (`redirect_hops`, `eTLD+1`, `scan_tools_used`, `scan_result_summary`, `file_download`, `authority_basis`, `platform_item_type`, `uploader`) so every score is reproducible.
- `docs/audits/link-audit-<date>.md` — summary counts + the remove/review list.
- Required repo files: `.github/CODEOWNERS`, `.github/pull_request_template.md`, `.github/ISSUE_TEMPLATE/resource-addition.md`, `SECURITY.md`, `contributing.md`, `.gitignore`.

## 7. Discoverability (ranking)

- Repo description (≤350 chars, keyword-rich): "Awesome list of Islamic resources: Qur'an, tafsir, hadith, seerah, tajweed, Arabic learning, apps, and books in English and Arabic."
- Topics (up to 20): `awesome`, `awesome-list`, `islam`, `islamic`, `quran`, `hadith`, `tafsir`, `seerah`, `tajweed`, `arabic`, `muslim`, `religion`. Currently only `awesome-list`/`islam`/`quran` are set — applying the full set (including `awesome`) is pending owner-approved repo settings. Default GitHub repository search matches **name + description + topics** — keep those keywords consistent. README content is found via code search / `in:readme` and by readers, not default repo search, so put the key terms in the description and topics first.
- Keep the Awesome badge and `## Contents` early in the README; update topics/description when categories change.

## 8. Hard rules

- No secrets, ever. No binary uploads except `header.jpg`-style assets with owner approval.
- No theology debates in commits/PRs — this is curation, not fatwa.
- Never claim a link is "virus-free" or "100% safe" — state the checks and score performed.
- Never merge, push, or change repo settings without explicit owner approval.

## 9. Current state (2026-11-04)

- `gh` auth: **JobeEnterprise**, ADMIN. Branch protection on `main` (1 review + CODEOWNERS) — owner PRs merge via `gh pr merge --admin`.
- Security: secret scanning + push protection **enabled**; private vulnerability reporting **enabled**; `main` protected. Dependabot/CodeQL off (Markdown-only).
- Merged: PR #11 (SOP/templates), PR #12 (README refresh), PR #13 (sweep additions). External PRs #8/#9/#10 closed as superseded by #12; issues #6/#7 closed as completed by #12.
- Contents: 87 unique links; 0 open PRs/issues. `awesome-lint README.md` passes with 0 errors (run with `GITHUB_TOKEN=$(gh auth token)` — unauthenticated runs miss the `awesome` topic).
- Pending README work (not done): none blocking. License is MIT; CC0 change needs owner decision.
