---
name: us-legal-research
description: |-
  US legal research — statute, regulation, and case law research
  methodology ported from Anthropic's claude-for-legal (Apache 2.0).
  Uses Federal Register API, web_search, and source-verified
  citation patterns. No external dependencies.
version: 1.0.0
author: copylawbot (ported from claude-for-legal by Anthropic)
tags: [legal, research, us-law, federal-register, regulation]
---

# US Legal Research

미국 법령 연구를 위한 스킬이야. [claude-for-legal](https://github.com/anthropics/claude-for-legal) 레포의 방법론을 Hermes Agent 포맷으로 이식했어.

## Available Data Sources

| Source | What | Auth | Cost | How to use |
|--------|------|------|------|------------|
| Federal Register API | US federal regulations, proposed rules, notices | None | Free | `curl` with URL encoding (see below) |
| Web search (built-in) | Case law, statutes, secondary sources | Varies | Free* | `web_search()` tool or `delegate_task(toolsets=["web"])` |
| r.jina.ai (recommended fallback) | Full-page content extraction for any URL | None | Free (rate-limited) | Prefix URL with `https://r.jina.ai/` |
| Manual URLs | GovInfo, Congress.gov, court websites | Varies | Free | `fetch_content()` or browser tools |

> \* `web_search` is free in most Hermes configurations. However, `web_extract` (for reading full page content) requires a configured backend (firecrawl, tavily, exa) — see **Optional API Integrations** below for the r.jina.ai zero-cost workaround.

---

## Optional API Integrations

The skill works **out of the box with zero API keys**. For enhanced research capabilities, these optional integrations can be configured:

### r.jina.ai — Free content extraction fallback

Hermes Agent's built-in `web_extract` tool requires a configured backend (firecrawl, tavily, or exa) with potential API costs. As a **free zero-config alternative**, prefix any URL with `https://r.jina.ai/`:

```bash
# Instead of web_extract which may not work:
# Use r.jina.ai directly:
curl -sL "https://r.jina.ai/https://www.courtlistener.com/opinion/12345"
```

This returns clean markdown with no API key needed. Use this when `web_extract` returns empty results or errors.

> **For Hermes Agent users:** To make `web_extract` work natively, set `extract_backend` in your `~/.hermes/config.yaml`:
> ```yaml
> web:
>   extract_backend: firecrawl  # or tavily, exa, parallel
> ```
> Then set the corresponding API key in `~/.hermes/.env`.

### CourtListener API — Verified case law citations

**Benefit:** CourtListener provides free, structured access to federal and state court opinions with verified citations. When connected, the skill can tag citations as `[CourtListener — verified]` instead of `[web search — verify]`.

**Setup:**

1. Register for a free API token at https://www.courtlistener.com/api/register/
2. Set the token in your Hermes environment:

```bash
# Add to ~/.hermes/.env
COURTLISTENER_API_KEY="your_token_here"
```

3. Then query via curl with source tagging:

```bash
# Search for AI copyright cases
curl -s "https://www.courtlistener.com/api/rest/v4/opinions/?q=AI+copyright&court__id=cafc" \
  -H "Authorization: Token $COURTLISTENER_API_KEY"
```

**Limitations:** Free tier has rate limits. Paid tiers remove limits. Some state courts may not be fully indexed.

### Hermes Config: Where API Keys Live

| File | Purpose | Example |
|------|---------|---------|
| `~/.hermes/.env` | API keys and secrets | `COURTLISTENER_API_KEY=..."` |
| `~/.hermes/config.yaml` | Tool and service configuration | `web.extract_backend: firecrawl` |
| `~/.hermes/skills/research/us-legal-research/` | This skill (SKILL.md + references) | Install via README instructions |

Environment variables from `.env` are automatically loaded by Hermes Agent and available in all sessions. No restart needed — run `/reload` in session or start a new session.

---

## Core Research Principles

1. **Source attribution on every citation** — never make a claim without tagging where it came from
2. **Separate observations from inferences** — mark what the source says vs. what you're concluding
3. **No silent supplement** — thin evidence = mark as `needs-verification`, never extrapolate
4. **Citation verification before reliance** — AI-generated citations can be fabricated
5. **State uncertainty explicitly** — every output is a draft, not a legal conclusion

### Source Tags

| Tag | Meaning |
|-----|---------|
| `[Federal Register]` | Retrieved from official FR API |
| `[web search — verify]` | Found via web search — higher fabrication risk |
| `[model knowledge — verify]` | From my training data — verify before relying |
| `[user provided]` | You gave me this info |
| `[secondary source]` | Commentary/aggregator, not primary source |

---

## Workflow 1: Federal Regulation Research

Search the Federal Register for federal agency rules, proposed rules, and notices.

### Search by keyword

```bash
# Note: URL-encode brackets as %5B and %5D in shell, or use --data-urlencode
curl -s "https://www.federalregister.gov/api/v1/documents.json?\
per_page=20&\
order=relevant&\
conditions%5Bterm%5D=${KEYWORD}&\
conditions%5Bagency_ids%5D%5B%5D=${AGENCY_ID}"
```

### Search by agency

Use the agency's numeric ID (not slug). Find IDs via the agencies endpoint:

```bash
curl -s "https://www.federalregister.gov/api/v1/agencies?per_page=200" | \
  python3 -c "import sys,json; [print(f'{a[\"id\"]}: {a[\"name\"]}') for a in json.load(sys.stdin).get('results',[]) if 'trade' in a['name'].lower()]"
```

Common agency IDs:
- FTC: `192`
- SEC: `466`
- CFPB: `573`
- DOL: `271`
- HHS: `221`
- FCC: `161`
- EPA: `145`
- DOJ: `268`

### Search by date range

```bash
curl -s "https://www.federalregister.gov/api/v1/documents.json?\
per_page=20&\
order=newest&\
conditions[publication_date][gte]=2026-01-01&\
conditions[publication_date][lte]=2026-05-16"
```

### Output format for a regulation digest

```markdown
## Regulation Digest — [date]

**Source:** [Federal Register API]

### 🔴 Always material
**[Agency] — [Title]**
- **Type:** [Final rule / Proposed rule / NPRM / Notice]
- **Summary:** [One-line]
- **Effective / Comment deadline:** [date]
- **Link:** [FR link]
- **Relevance:** [why this matters]

### 🟡 Review-worthy
[Same format, for items needing assessment]

### 📝 FYI
[N items — titles only]
```

---

## Workflow 2: Source-Verified Citation Pattern

When citing a legal source, follow this format:

### For statutes
```
[Statute] — [Title] § [Section] (Year)
[web search — verify] or [user provided]
```

### For regulations
```
[Regulation] — [Title] § [Section] ([Year])
[Federal Register] or [web search — verify]
```

### For case law
```
[Case Name], [Volume] [Reporter] [Page] ([Court] [Year])
[web search — verify]
```

### Reviewer Note (prepend to every research output)

```markdown
> **⚠️ Reviewer note**
> - **Sources:** [tool used — verified / unverified]
> - **Flagged for your judgment:** [N items marked [verify] | none]
> - **Currency:** [searched for developments since [date] — found N updates | could not search]
> - **Before relying:** [the 1-2 things to check first]
```

---

## Workflow 3: Legal Document Templates

### Claim/Element Chart

For mapping legal claims or defenses to evidence:

```markdown
| [#] | Element | Evidence supporting (pin-cited) | Evidence contradicting | Strength | State |
|-----|---------|-------------------------------|----------------------|----------|-------|
| 1 | [element] | [source, pin cite] | [contra] | strong/moderate/weak/none | supported/partial/disputed/gap |
```

Always end with:
- **Gap list** — elements with thin or no evidence (the priority output)
- **Conclusion line:** *This chart is a draft, not a finding.* Attorney must verify every cite.

### Chronology/Timeline

```markdown
| Date | Event | Significance | Sources |
|------|-------|-------------|---------|
| YYYY-MM-DD | [what happened, one sentence] | 🔴/🟡/⚪ | [source path] |
```

- 🔴 Key event — moves the analysis
- 🟡 Relevant context
- ⚪ Background only

### Research Brief

```markdown
## Briefing: [Topic]

**Status:** [current state of research]
**Last updated:** [date]

### Summary
[One-paragraph overview]

### Findings
[Organized by question or theme, with source tags]

### Open Questions
[What remains unresolved]

### Sources
[Numbered list with direct URLs and source tags]
```

---

## Workflow 4: Legal Research Loop (with Goal Integration)

When conducting legal research as part of an active `/goal`:

1. **Set the goal** — `/goal Research [specific legal question]`
2. **Gather sources** — Federal Register API + web_search
3. **Synthesize** — extract relevant findings with source tags
4. **Validate** — check if the goal is achieved (have we answered the question?)
5. **Iterate or deliver** — if more sources needed, loop; if answered, present findings

After each research cycle, run `/goal validate` to check progress.

---

## Critical: web_search Availability

`web_search` may not be a direct tool in all sessions (depends on the current provider or config). **Do NOT treat this as a blocker.** Fallback is reliable:

### Fallback: delegate_task with web toolset

```python
# When web_search is not directly available, delegate to a subagent:
delegate_task(
    goal="Research [legal question]: search for case law, statutes, commentary",
    toolsets=["web"]  # grants the subagent web_search access
)
```

The subagent gets its own `web_search` tool even when the parent session doesn't have one. This is the canonical workaround — always use this instead of browser navigation or bare terminal for legal research.

### When to use which tool
- **delegate_task(toolsets=["web"])** — FIRST CHOICE for case law, statute, and regulation research; spawns a subagent with web_search
- **Federal Register API** (curl via terminal) — for structured federal regulation data (document type, agency, effective date, comment deadline). Lower fabrication risk than web search.
- **r.jina.ai reader** — for reading specific articles, statutes, or regulatory text pages after the subagent returns URLs. Prefix with `https://r.jina.ai/` to get clean markdown. Free, no API key needed.
  > Note: Hermes Agent's built-in `fetch_content` / `web_extract` tools require a configured `extract_backend` (firecrawl, tavily, exa) in `~/.hermes/config.yaml`. Without one, they fall back to DuckDuckGo (search only, not extraction). r.jina.ai works as a zero-config drop-in replacement.
- **Browser tools** — LAST RESORT; only when other methods fail and a specific website requires JS rendering

### Output conventions
- Every research output gets a **reviewer note** at the top
- Every citation gets a **source tag** (what tool produced it)
- Every conclusion is marked `[draft — attorney review required]`
- Save to `outputs/` directory with slug-based naming
- Write `.provenance.md` sidecar listing sources and verification status

### Reference files in this skill
- `references/open-source-trade-secret-cases.md` — compiled US case law on open source code vs. trade secret (Salerno v. Scialli, SCO v. IBM, Kalda, Bunner, etc.). Research conducted via `delegate_task(toolsets=["web"])` fallback.

### Key caution patterns
- AI-generated legal citations are **sometimes fabricated** — always verify against primary source
- CourtListener requires API token (free registration) — note as limitation if not configured
- Federal statutes (US Code) not directly searchable via API without Congress.gov key — use web_search + govinfo.gov as fallback
- State laws vary significantly — always specify jurisdiction in research scope
