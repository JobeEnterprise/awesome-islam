# Contributing to Awesome Islam

Thanks for helping curate this list. **One resource per pull request.**

## Adding a resource

1. Pick the best-fit section (Quran, Tajweed, Tafsir, Arabic, Seerah, Hadith, General Resources). Don't put items in `Needs Categorization`.
2. Add it in **alphabetical order** within the section (maintainer convention), in this format:

   ```md
   - [Resource Name](https://example.com/resource) - One-line description of what it is and why it's worth including. `Tag`
   ```

   Tags: `Book`, `PDF`, `YouTube Channel`, `Website`, `iOS`/`Android` (only when true). Descriptions must end with a period.
3. Check for duplicates first — same resource, different mirror: keep the canonical URL (strip `www.`, trailing slash, tracking parameters).

## Link requirements

Your link must pass all of these (maintainers verify before merge):

- Resolves with HTTP 200; the final URL is the resource itself.
- HTTPS only. No URL shorteners, no IP-literal URLs, no executables/archives.
- Legitimate: official site, scholar, publisher, institution, or established platform. No impersonation or lookalike domains.
- High quality: accurate title/description, correct category, still maintained.
- File downloads (PDF/EPUB): accepted only from trusted publishers/libraries and get manual review.

## Content provenance & AI disclosure

- If the resource is AI-generated, AI-assisted, or AI-translated, say so in the PR and show where the site discloses it. If a scholar reviewed it, name them and link their credentials.
- If you are the author, owner, or affiliate of the resource, declare it.
- Do not claim scholar review, authenticity, `verified tafsir/hadith`, or `fatwa` value without pasted evidence.
- Paraphrase, don't copy authority claims from the site. Where AI generation is material, the README line must disclose it (e.g. `Website (AI-generated glosses; meanings not scholar-reviewed)`).

## PR checklist

- [ ] One resource, one PR
- [ ] Alphabetical order, correct section, description ends with a period
- [ ] No duplicate (canonical URL checked)
- [ ] Link resolves over HTTPS and passes the requirements above
- [ ] AI-generated/assisted/translated? Disclosed with source URL, or `No`
- [ ] Scholar reviewer named with credentials, or `None`
- [ ] I am / am not the author, owner, or affiliate of this resource
- [ ] Evidence pasted in PR per SOP §4

Maintainers run the checks in `SOP.md` (§2–§3) before merging. There is no auto-merge.
