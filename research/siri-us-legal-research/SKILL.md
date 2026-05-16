---
name: siri-us-legal-research
description: |-
  US legal research for Siri — statute, regulation, and case law research
  methodology absorbed from claude-for-legal (Anthropic's open-source legal
  AI repo). Uses Federal Register API, web_search, and source-verified
  citation patterns. No external dependencies.
version: 1.0.0
author: Siri (absorbed from claude-for-legal)
tags: [legal, research, us-law, federal-register, regulation]
---

# Siri US Legal Research

미국 법령 연구를 위한 스킬이야. claude-for-legal 레포의 방법론을 완전 흡수했어.

## Available Data Sources

| Source | What | Auth | How to use |
|--------|------|------|------------|
| Federal Register API | US federal regulations, proposed rules, notices | Free, no key | `curl -s "https://www.federalregister.gov/api/v1/documents?..."` |
| Web search | Case law, statutes, secondary sources | Free | `web_search()` tool |
| Manual URLs | GovInfo, Congress.gov, court websites | Varies | `fetch_content()` |

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
curl -s "https://www.federalregister.gov/api/v1/documents.json?\
per_page=20&\
order=relevant&\
conditions[term]=${KEYWORD}&\
conditions[agency_ids][]=${AGENCY_ID}"
```

### Search by agency

Common agency slugs:
- FTC: `federal-trade-commission`
- SEC: `securities-and-exchange-commission`
- CFPB: `consumer-financial-protection-bureau`
- DOL: `labor-department`
- HHS: `health-and-human-services-department`
- FCC: `federal-communications-commission`
- EPA: `environmental-protection-agency`
- DOJ: `justice-department`

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
- **fetch_content** — for reading specific articles, statutes, or regulatory text pages after the subagent returns URLs
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
