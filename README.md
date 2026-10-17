# LastSearch Skills

Agent skills for [LastSearch](https://lastsearch.ai) — evidence-backed web research for AI coding agents.

Works with **Claude Code, Codex CLI, Gemini CLI, Cursor, Copilot, Windsurf**, and any tool that supports the [Agent Skills specification](https://agentskills.io/specification).

## Quick Install

```bash
npx skills add LastSearch-HQ/lastsearch-skills
```

Or manually copy any skill folder into your agent's skills directory (e.g., `~/.claude/skills/`).

## Available Skills

| Skill | What it does | Key tool |
|-------|-------------|----------|
| [research](research/) | Evidence-backed answers with citations and confidence scores | `answer` |
| [fact-check](fact-check/) | Compare raw LLM vs evidence-backed answers, verify claims | `compare` |
| [extract](extract/) | Extract structured claims and quotes from any URL | `extract` |
| [sessions](sessions/) | Multi-query research with persistent knowledge accumulation | `session_*` |
| [deep-dive](deep-dive/) | Multi-step agentic research with reasoning chains and gap analysis | `answer` (deep) |
| [compare-claims](compare-claims/) | Settle factual disputes — evidence-backed vs raw LLM side-by-side | `compare` |
| [monitor](monitor/) | Track evolving topics over time, diff against prior knowledge | `session_*` |
| [cite](cite/) | Generate properly formatted citations (APA/MLA) with authority scores | `answer` (thorough) |
| [clarity](clarity/) | Anti-hallucination answer engine — three modes: prompt, answer, verified | `clarity` |
| [verify-document](verify-document/) | Fact-check an entire document — extracts every claim, verifies each, returns A-F grade | `verify_document` |
| [verify](verify/) | "Is this true?" — verify any claim, URL, file, or pasted AI answer; every claim judged with the exact evidence span shown (`npx verify-anything`) | `verify` |

## Prerequisites

These skills use LastSearch's MCP server. Add it to your MCP config:

```json
{
  "mcpServers": {
    "lastsearch": {
      "command": "npx",
      "args": ["-y", "lastsearch"]
    }
  }
}
```

For session features (persistent research memory, sharing, forking), set a LastSearch API key:

```json
{
  "mcpServers": {
    "lastsearch": {
      "command": "npx",
      "args": ["-y", "lastsearch"],
      "env": {
        "LASTSEARCH_API_KEY": "ls_xxx"
      }
    }
  }
}
```

Get a free API key at [lastsearch.ai/dashboard](https://lastsearch.ai/dashboard).

## How Skills Work

Each skill is a `SKILL.md` file that teaches AI coding agents **when and how** to use LastSearch tools. Unlike the MCP server (which provides the tools), skills provide the **judgment layer** — they tell agents when to trigger, what parameters to use, and how to interpret results.

```
User asks a question
     ↓
Agent reads SKILL.md → knows when to trigger
     ↓
Agent calls MCP tools (answer, compare, etc.)
     ↓
Agent interprets results using skill guidance
     ↓
User gets cited, evidence-backed answer
```

## What is LastSearch?

Open-source research infrastructure for AI agents. Every answer goes through a verification pipeline:

1. **Web Search** — Tavily API finds relevant pages
2. **Page Fetch** — Downloads and parses pages into clean text
3. **Claim Extraction** — AI extracts structured claims with source attribution
4. **BM25 Verification** — Sentence-level matching verifies each claim
5. **Cross-Source Consensus** — Claims in multiple sources get higher scores
6. **Contradiction Detection** — Conflicting claims are flagged
7. **Domain Authority** — 10,000+ domains scored with Bayesian dynamic blending
8. **Confidence Score** — 8-factor evidence-based score (not LLM self-assessed)

Available as [MCP server](https://www.npmjs.com/package/lastsearch), [REST API](https://lastsearch.ai/docs), and [Python SDK](https://pypi.org/project/lastsearch/).

## Contributing

Want to add a new skill? PRs welcome!

1. Create a new folder with a descriptive name (e.g., `competitive-analysis/`)
2. Add a `SKILL.md` following the format of existing skills
3. Include: when to trigger, prerequisites, step-by-step workflow, examples, tips
4. Submit a PR to the `shreyas` branch

## Links

- [LastSearch](https://lastsearch.ai)
- [Documentation](https://lastsearch.ai/docs)
- [Main Repo](https://github.com/LastSearch-HQ/LastSearch-Dev)
- [MCP Server (npm)](https://www.npmjs.com/package/lastsearch)
- [Python SDK (PyPI)](https://pypi.org/project/lastsearch/)
- [Discord](https://discord.gg/ubAuT4YQsT)

## License

MIT
