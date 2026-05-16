# ⚖️ oh-my-hermes-for-legal-researcher

**US legal research skill for [Hermes Agent](https://github.com/NousResearch/hermes-agent)** — statute, regulation, and case law research methodology ported from [Anthropic's claude-for-legal](https://github.com/anthropics/claude-for-legal) project.

> Built with **zero external dependencies** and **no API keys required**. All data sources used (Federal Register API, web search) are free and open.

> 🌐 [Readme in Korean (한국어)](./README.ko.md)

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
