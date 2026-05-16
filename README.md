# ⚖️ oh-my-hermes-for-legal-researcher

**US legal research skill for [Hermes Agent](https://github.com/NousResearch/hermes-agent)** — statute, regulation, and case law research methodology ported from [Anthropic's claude-for-legal](https://github.com/anthropics/claude-for-legal) project.

> Built with **zero external dependencies** and **no API keys required**. All data sources used (Federal Register API, web search) are free and open.

> 🇰🇷 [한국어](./README.ko.md)

---

## 📌 What You Can Find

### ✅ Federal Regulations
Search the **Federal Register** — the official daily journal of the U.S. federal government.

| Source | Coverage | How |
|--------|----------|-----|
| **Proposed rules** | Rules agencies propose for public comment | Federal Register API → structured Regulation Digest |
| **Final rules** | Adopted rules with effective dates | Same |
| **Notices** | Agency notices, consent orders, information collections | Same |
| **NPRMs** (Notice of Proposed Rulemaking) | Formal proposals with comment deadlines | Same |

**Agencies covered:** FTC, SEC, CFPB, FCC, EPA, DOJ, DOL, HHS, and 200+ others via numeric ID lookup.

### ✅ Case Law
Search U.S. federal and state court opinions via web search.

| Type | Coverage | Limitation |
|------|----------|------------|
| **Federal cases** | Supreme Court, Circuit Courts, District Courts | Citations tagged `[verify]` — always cross-check |
| **State cases** | Published state appellate opinions | Not all states equally covered |
| **Key topic areas** | AI & copyright, open source & trade secret, privacy, antitrust | Just ask |

> 🔌 **Optional:** Connect the [CourtListener API](#courtlistener-api--verified-citations) (free) for verified citations instead of `[verify]` tags.

### ✅ Statutes
Search U.S. Code, public laws, and bills via web search.

| Type | Coverage | Limitation |
|------|----------|------------|
| **U.S. Code** (USC) | Federal statutes by title and section | Web search only — no dedicated API without Congress.gov key |
| **Public Laws** | Recently enacted legislation | Via govinfo.gov |
| **Bills** | Pending legislation | Via Congress.gov |

### ✅ Secondary Sources
Search law review articles, legal commentary, and regulatory analysis via web search.

---

### ❌ Not Covered (Limitations)

| What | Why |
|------|-----|
| **Non-U.S. law** | US law only by design |
| **Paywalled databases** (Westlaw, LexisNexis) | No paid subscriptions — free/open sources only |
| **State-specific statutes** | Web search can find them but no structured API |
| **Very recent unreported cases** | Takes time to appear in public databases |
| **Legal advice or case strategy** | Disclaimer: every output is a draft for attorney review |

---

## 🧬 About This Port

[claude-for-legal](https://github.com/anthropics/claude-for-legal) by [Anthropic](https://www.anthropic.com/) is a comprehensive open-source reference implementation of AI-assisted legal workflows. It spans **80+ named agents** across **11+ practice-area plugins**:

```
claude-for-legal/
├── commercial-legal/        # Contract review, renewals, escalations
├── corporate-legal/         # M&A diligence, closing checklists
├── employment-legal/        # Hire/term review, worker classification
├── privacy-legal/           # DPA, DSAR, PIA
├── product-legal/           # Launch review, marketing claims
├── regulatory-legal/        # Reg feed watcher, policy diff
├── ai-governance-legal/     # AI use case triage, impact assessment
├── ip-legal/                # Trademark, FTO, DMCA, OSS
├── litigation-legal/        # Claim charts, chronologies, deposition prep
├── legal-clinic/            # Law school clinic workflows
├── law-student/             # Socratic drilling, IRAC, bar prep
├── legal-builder-hub/       # Community skill discovery & install
│
└── [Shared Research Methodology Layer] ← 🎯 **What we ported**
    ├── Source tagging & citation verification patterns
    ├── Reviewer Note conventions
    ├── Uncertainty disclosure principles
    └── Research loop (gather → synthesize → validate → deliver)
```

**We extracted only the shared research methodology layer from claude-for-legal — the core that underlies all 11 plugins — and ported it to a Hermes Agent skill.** Practice-domain logic (contract review, diligence, employment, privacy, etc.) is intentionally excluded. This skill focuses on one thing: **finding and analyzing statutes, regulations, case law, and legal scholarship with source-verified methodology.**

This skill is designed for:
- **Legal researchers** — searching and structuring statutes, regulations, case law
- **Law students** — IRAC analysis, claim charts, research briefs
- **Practitioners** — quick initial research with verification flags surfaced
- **AI/policy researchers** — exploring regulatory frameworks and monitoring Federal Register updates

Where claude-for-legal is a comprehensive practice-area suite (agents, MCP connectors, scheduled watchers, practice profiles), `oh-my-hermes-for-legal-researcher` is a focused research skill that gets you reliable, source-verified US legal research with zero configuration and no paid subscriptions.

---

## 📋 What Was Ported from claude-for-legal

### ✅ Ported — Core Research Methodology

| Concept | Original Source in claude-for-legal | Our Implementation |
|---------|--------------------------------------|-------------------|
| **Source attribution on every citation** | Core principle across all plugins — every claim tagged with provenance | `[Federal Register]`, `[web search — verify]`, `[model knowledge — verify]`, `[user provided]`, `[secondary source]` tags |
| **Separate observations from inferences** | `claude-for-legal/README.md` — "mark what the source says vs. what you conclude" | Explicit distinction in all research output: source claim vs. analytical conclusion |
| **No silent supplement** | "Thin evidence = mark `needs-verification`, never extrapolate" | `needs-verification` flag in all research outputs; gaps surfaced explicitly |
| **Citation verification before reliance** | "AI-generated citations can be fabricated — verify every cite" | Reviewer Note at top of every output flags unverified sources |
| **Reviewer Note pattern** | Prepend to every research deliverable — sources used, flags, currency, what to check first | Structured Reviewer Note: sources, flags, jurisdiction scope, currency check |
| **State uncertainty explicitly** | "Every output is a draft, not a legal conclusion" | `[draft — attorney review required]` marker on every deliverable |

### ✅ Ported — Legal Document Templates

| Template | Original Source | Our Adaptation |
|----------|----------------|----------------|
| **Claim/Element Chart** | `litigation-legal` plugin — claim-chart skill | Element-by-element chart with evidence columns, strength ratings, gap list |
| **Chronology/Timeline** | `litigation-legal` plugin — chronology-builder skill | Date-ordered event timeline with significance markers (🔴/🟡/⚪) |
| **Research Brief** | Cross-plugin standard output format | Summary / Findings / Open Questions / Sources structure with numbered citations |

### ✅ Ported — Regulatory Research Workflow

| Workflow | Original Source | Our Implementation |
|----------|----------------|----------------|
| **Federal Regulation Search** | `regulatory-legal` plugin — `Reg Feed Watcher` agent + `On-demand Reg Check` command | Federal Register API search by keyword, agency, date range |
| **Regulation Digest format** | `regulatory-legal` output convention — 🔴 Always material / 🟡 Review-worthy / 📝 FYI | Same triage format with effective dates, comment deadlines, relevance assessment |
| **Agency-specific search** | `regulatory-legal` plugin — agency configurations | Slugs for 8+ agencies (FTC, SEC, CFPB, DOL, HHS, FCC, EPA, DOJ) |

### ✅ Ported — Research Principles & Guardrails

- **Disclaimer pattern** — Every output is a draft for attorney review (from claude-for-legal's `[!IMPORTANT]` callout in README)
- **Citation fabrication awareness** — Web search results are tagged `[verify]`; no unverified claim is presented as fact (core guardrail across all claude-for-legal plugins)
- **Research iteration loop** — Gather → Synthesize → Validate → Iterate/Deliver (mirrors the research loop in all claude-for-legal practice plugins)

### ✅ Ported — Case Law Research Strategy

- **Web-based case law search** via structured queries (corresponds to claude-for-legal's CourtListener MCP connector pattern, but using free web search as fallback)
- **Source-verified citation format** — Case Name, Volume Reporter Page (Court Year) — standard Bluebook-style format used across claude-for-legal

---

## 🔲 Out of Scope (Not Ported)

These are **intentionally excluded** because they depend on paid subscriptions, MCP connectors, platform-specific features, or are beyond the scope of a focused research skill:

| Feature | In claude-for-legal | Why Not Here |
|---------|---------------------|--------------|
| Practice-area plugins (commercial, corporate, employment, privacy, IP, litigation, AI governance) | 11+ separate plugin directories | Hermes Agent uses a flat skill namespace; practice-specific logic belongs in separate skills |
| MCP connectors (CourtListener, Trellis, Ironclad, DocuSign, iManage, Everlaw, Box, etc.) | `.mcp.json` per plugin + MCP server references | Requires paid subscriptions / API keys; this skill is designed for zero-cost operation |
| Cold-start interview mechanism | `/<plugin>:cold-start-interview` writes practice profile to `CLAUDE.md` | Hermes Agent uses memory + goal tracking instead of file-based practice profiles |
| Scheduled agents (Renewal Watcher, Docket Watcher, Reg Feed Watcher, etc.) | `agents/` directory with cron schedule in frontmatter | Use Hermes Agent's built-in cronjob system for scheduling |
| Microsoft 365 integration (Word tracked changes, Excel workbooks) | Claude for Microsoft 365 add-in | Platform-specific; Hermes Agent is terminal/API-first |
| Practice profile (`CLAUDE.md`) | Per-plugin config file read by every skill | Hermes Agent skills read from memory and user profile instead |
| Legal Builder Hub (community skill discovery, trust layer, QA framework) | `legal-builder-hub` plugin | Beyond scope of a single skill; may be explored separately |
| Managed Agent deployment (`agent.yaml`, leaf-worker subagents) | `managed-agent-cookbooks/` directory | Hermes Agent has its own deployment model |
| Thomson Reuters CoCounsel integration | `external_plugins/cocounsel-legal/` | Paid subscription required |

---

## 🔧 How It Works

This skill is a **methodology guide for the AI agent** (Hermes Agent), not a standalone application. When you ask a legal research question, the agent follows the workflows in `SKILL.md` and calls Hermes Agent's built-in tools. Here is exactly how that works:

### Tool Architecture

| Tool | What it does | Works out of the box? | Notes |
|------|-------------|----------------------|-------|
| `delegate_task(toolsets=["web"])` | Spawns a subagent with web search capability | ✅ Yes | Primary method for case law / statute research |
| `curl` + Federal Register API | Structured regulation data via REST API | ✅ Yes | No auth needed. Use `%5B`/`%5D` for bracket params |
| r.jina.ai `https://r.jina.ai/URL` | Full-page markdown from any URL | ✅ Yes | Free, no key. Preferred for reading article/statute text |
| `browser_navigate` + `browser_snapshot` | Headless Chromium page rendering | ✅ Yes | Fallback for JS-heavy or login-gated pages |
| Hermes Agent `web_extract` | URL content extraction | ⚠️ Needs config | Requires `extract_backend` in config.yaml. **Default: DuckDuckGo (search only)** |
| Hermes Agent `fetch_content` | Same as web_extract | ⚠️ Needs config | Same backend requirement |

### The DuckDuckGo Default (Important)

Hermes Agent ships with `extract_backend: ''` (empty) in its default configuration. When `web_extract` is called with an empty backend, it falls back to **DuckDuckGo** — which is a **search engine, not a page content extractor**. This means:

```python
# ❌ web_extract may return empty or error — depends on user's Hermes config
web_extract(urls=["https://example.com/case-law"])

# ✅ r.jina.always works — no config needed
curl -sL "https://r.jina.ai/https://example.com/case-law"

# ✅ Browser always works — renders the page like Chrome would
browser_navigate("https://example.com/case-law")
browser_snapshot(full=true)
```

**This skill does NOT recommend or depend on DuckDuckGo for content extraction.** The skill's documented workflows use:

1. **r.jina.ai** as the primary extraction method (free, no API key needed)
2. **Browser tools** (Chromium) as fallback for complex pages
3. **Federal Register API** directly via curl for regulation data

### Content Extraction Fallback Chain

```
Fetch URL content
│
├─ r.jina.ai reader ────────── PREFERRED
│  curl -sL "https://r.jina.ai/<URL>"
│  → Returns clean markdown. Free, no API key.
│
├─ Browser tools (Chromium) ── FALLBACK
│  browser_navigate(url) → browser_snapshot(full=true)
│  → For JS-heavy SPAs, login walls, complex layouts.
│
└─ web_extract / fetch_content ── ONLY IF CONFIGURED
   Requires extract_backend in ~/.hermes/config.yaml
   + corresponding API key in ~/.hermes/.env
```

### What You Need to Know Before Installing

| Question | Answer |
|----------|--------|
| Do I need to configure anything? | **No.** Just copy the skill folder. |
| Does DuckDuckGo affect normal operation? | **No.** This skill doesn't rely on `web_extract`. |
| Does it work without API keys? | **Yes.** Federal Register API (free), r.jina.ai (free), browser tools (no key). |
| Can I improve it with API keys? | **Yes.** Add CourtListener for verified case citations. |
| What if r.jina.ai fails? | Browser tools handle any page a real browser can render. |

### Where Configuration Files Live

| File | Purpose | Managed by |
|------|---------|-----------|
| `~/.hermes/config.yaml` | Hermes Agent settings (model, tools, backends) | Hermes / User |
| `~/.hermes/.env` | API keys and secrets | User |
| `~/.hermes/skills/research/us-legal-research/` | This skill (SKILL.md + references) | This project |
| `~/.hermes/sessions/` | Session transcripts | Hermes |

---

## 🛠️ Installation

### Prerequisites

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) installed and configured
- No API keys, no paid subscriptions, no MCP servers required

### Install

```bash
git clone https://github.com/charliehotel/oh-my-hermes-for-legal-researcher.git
mkdir -p ~/.hermes/skills/research
cp -r oh-my-hermes-for-legal-researcher/research/us-legal-research ~/.hermes/skills/research/
```

### Verify

```bash
ls ~/.hermes/skills/research/us-legal-research/
# Should show: SKILL.md  references/
```

---

## 🔌 Optional API Integrations

The skill works **out of the box with zero API keys**. These optional integrations enhance research quality but are not required.

### r.jina.ai — Free content extraction

Hermes Agent's built-in `web_extract` may not work without a configured backend (firecrawl, tavily, or exa). As a **free zero-config alternative**, prefix any URL with `https://r.jina.ai/`:

```bash
curl -sL "https://r.jina.ai/https://www.courtlistener.com/opinion/12345"
```

No API key needed. Returns clean markdown. Use this when `web_extract` returns empty results.

> To make `web_extract` work natively, set in `~/.hermes/config.yaml`:
> ```yaml
> web:
>   extract_backend: firecrawl  # or tavily, exa, parallel
> ```
> Then add the API key to `~/.hermes/.env`.

### CourtListener API — Verified citations

**Benefit:** Structured access to federal/state court opinions with verified citations. When connected, citations get tagged `[CourtListener — verified]` instead of `[web search — verify]`.

**Setup:**

```bash
# 1. Register: https://www.courtlistener.com/api/register/
# 2. Add to ~/.hermes/.env:
COURTLISTENER_API_KEY="your_token_here"

# 3. Query with source tagging:
curl -s "https://www.courtlistener.com/api/rest/v4/opinions/?q=AI+copyright" \
  -H "Authorization: Token $COURTLISTENER_API_KEY"
```

Environment variables from `.env` are auto-loaded by Hermes Agent. Run `/reload` or start a new session to pick up changes.

---

## 🚀 Usage

Once installed, Hermes Agent loads the skill automatically on relevant queries. Examples:

**"Search the Federal Register for FTC proposed rules on AI from 2026"**
→ Calls Federal Register API → returns structured Regulation Digest with source tags

**"Find recent case law on open source code and trade secret"**
→ Spawns a subagent with web_search → returns findings with `[verify]` tags → includes Reviewer Note

**"Build a chronology from these deposition dates"**
→ Uses the chronology template with significance markers

**"Research the legal framework for AI governance in healthcare"**
→ Runs the full research loop: gather sources → synthesize → flag gaps → deliver with Reviewer Note

### How the Research Loop Works

```
   ┌─────────────┐
   │ Set goal    │  (define the research question)
   └──────┬──────┘
          ▼
   ┌─────────────┐
   │ Gather      │  (Federal Register API + web_search)
   └──────┬──────┘
          ▼
   ┌─────────────┐
   │ Synthesize  │  (extract findings with source tags)
   └──────┬──────┘
          ▼
   ┌─────────────┐     ┌─────────────┐
   │ Validate    │────→│ More needed?│──→ loop
   └──────┬──────┘     └─────────────┘
          ▼ no
   ┌─────────────┐
   │ Deliver     │  (with Reviewer Note + source tags)
   └─────────────┘
```

### Output Convention

Every research deliverable includes:

```markdown
> **⚠️ Reviewer note**
> - **Sources:** Federal Register API / delegated web search
> - **Flagged for your judgment:** [N items marked `[verify]` | none]
> - **Currency:** [last checked date]
> - **Before relying:** [the 1-2 things to verify first]

[Research content with source tags throughout]

*[draft — attorney review required]*
```

---

## 📁 Project Structure

```
oh-my-hermes-for-legal-researcher/
├── LICENSE                                    # Apache 2.0
├── README.md                                  # ← you are here
└── research/
    └── us-legal-research/
        ├── SKILL.md                           # Main skill: methodology & workflows
        └── references/
            └── open-source-trade-secret-cases.md  # Compiled case reference
```

---

## 📜 License & Attribution

**Apache 2.0** — see [LICENSE](./LICENSE).

This project is derived from **[claude-for-legal](https://github.com/anthropics/claude-for-legal)** by **[Anthropic](https://www.anthropic.com/)** (Apache 2.0). The original work is copyright Anthropic.

Portions adapted from the following claude-for-legal components:
- Core research methodology (source attribution, verification, uncertainty disclosure)
- Regulatory research workflow (`regulatory-legal` plugin patterns)
- Litigation document templates (`litigation-legal` plugin — claim charts, chronologies)
- Reviewer Note and disclaimer conventions (cross-plugin standards)

Hermes Agent adaptation and additional workflows by **copylawbot**.

---

## 🤝 Contributing

PRs welcome! Areas for contribution:

- Additional legal reference cases and citations
- Support for other free legal APIs (GovInfo, Congress.gov when key available)
- Workflow improvements for specific practice areas
- Additional document templates

Please retain the Apache 2.0 license and attribution to the original claude-for-legal project.

---

> **Disclaimer:** This tool assists with legal research but does not provide legal advice. All outputs are drafts requiring attorney review. AI-generated citations may be fabricated — verify every cite against primary sources before reliance.
