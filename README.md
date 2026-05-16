# ⚖️ oh-my-hermes-for-legal

**US legal research skills for [Hermes Agent](https://github.com/NousResearch/hermes-agent)** — statute, regulation, and case law research using free, no-API-key-required data sources.

> 🧠 This is a **Hermes Agent skill port** of Anthropic's [claude-for-legal](https://github.com/anthropics/claude-for-legal) project. All legal research methodology, workflows, and best practices are absorbed from the original. Licensed under **Apache 2.0**.

---

## ✨ What's Included

| Skill | Description |
|-------|-------------|
| `research/siri-us-legal-research` | Full legal research workflow: Federal Regulation API, citation-verified case law, statute research, source-tagged output, reviewer notes |

### Workflows

- **Federal Regulation Research** — Search the Federal Register API (free, no key) for proposed rules, final rules, agency notices
- **Source-Verified Citation Pattern** — Every citation tagged with provenance (`[Federal Register]`, `[web search — verify]`, etc.)
- **Legal Document Templates** — Claim/element charts, chronology/timelines, research briefs
- **Case Law Research** — Via `web_search` fallback (subagent delegation pattern)
- **Goal-Integrated Research Loop** — Works with Hermes Agent's `/goal` system for long-running research objectives

### Key Principles

- **Source attribution on every citation** — no claim without a tag
- **Separate observations from inferences** — mark what the source says vs. what you conclude
- **No silent supplement** — thin evidence = marked `needs-verification`
- **Citation verification before reliance** — AI-generated citations can be fabricated

---

## 🛠️ Installation

### Prerequisites

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) installed and configured
- No API keys required (Federal Register API is free and open)

### Option A: Manual install (recommended)

```bash
git clone https://github.com/charliehotel/oh-my-hermes-for-legal.git
mkdir -p ~/.hermes/skills/research
cp -r oh-my-hermes-for-legal/research/siri-us-legal-research ~/.hermes/skills/research/
```

### Option B: Symlink (for active development)

```bash
git clone https://github.com/charliehotel/oh-my-hermes-for-legal.git ~/.hermes/workspace/oh-my-hermes-for-legal
ln -s ~/.hermes/workspace/oh-my-hermes-for-legal/research/siri-us-legal-research ~/.hermes/skills/research/siri-us-legal-research
```

---

## 🚀 Usage

Once installed, the skill is automatically loaded by Hermes Agent when the conversation context matches. You can also load it manually:

In a Hermes Agent session, the skill is activated by the `siri-us-legal-research` skill ID. Just ask Siri to:

```
"Find me the latest FTC proposed rules on AI"
"Research trade secret case law related to open source code"
"What are the current FCC regulations on robocalls?"
```

### Example: Federal Regulation Search

```
=> Search the Federal Register for SEC climate disclosure rules from 2026
```

The skill autonomously:
1. Calls the Federal Register API via curl
2. Returns a structured Regulation Digest with source tags
3. Includes a Reviewer Note with verification flags

### Example: Case Law Research

```
=> Find recent case law on open source code and trade secret misappropriation
```

The skill:
1. Spawns a subagent with `web_search` access (no direct tool dependency)
2. Searches for relevant cases
3. Returns findings with `[web search — verify]` tags
4. Includes a Reviewer Note flagging anything that needs manual verification

---

## 📁 Project Structure

```
oh-my-hermes-for-legal/
├── LICENSE               # Apache 2.0
├── README.md             # ← you are here
└── research/
    └── siri-us-legal-research/
        ├── SKILL.md      # Main skill file — methodology & workflows
        └── references/
            └── open-source-trade-secret-cases.md  # Compiled case reference
```

---

## 📜 License & Attribution

**Apache 2.0** — see [LICENSE](./LICENSE).

This project is a **Hermes Agent skill port** derived from **[claude-for-legal](https://github.com/anthropics/claude-for-legal)** by [Anthropic](https://www.anthropic.com/). The original work is copyright Anthropic and licensed under Apache 2.0.

Modifications and Hermes Agent adaptation by **copylawbot**.

> **Disclaimer:** This tool assists with legal research but does not provide legal advice. All outputs are drafts requiring attorney review. AI-generated citations may be fabricated — verify every cite against primary sources before reliance.

---

## 🤝 Contributing

PRs welcome! Whether it's:
- Adding more reference cases
- Improving workflows for specific practice areas
- Adding support for other free legal APIs

Please retain the Apache 2.0 license and attribution to the original claude-for-legal project.
