---
name: verify
description: "Is this true?" — verify any claim, paragraph, URL, file, or AI-generated answer against live web sources before trusting or shipping it. Use when the user asks whether something is true, pastes an AI answer to check, or when you are about to deliver factual statements and want to self-check them first.
---

# verify — is this true?

One tool, one question: *is this true?* Every factual claim in the input is judged against live web
evidence and comes back **verified / contradicted / unverified** with a confidence score, the source,
the reason, and the exact evidence span the verifier judged — so the answer can be checked, not just trusted.

Built on the open [LastSearch](https://lastsearch.ai) verification engine
(source: [LastSearch-HQ/verify](https://github.com/LastSearch-HQ/verify), Apache-2.0).

## When to Use

- User asks "is this true?", "is this right?", "check this", "fact-check this", "did I get this right?"
- User pastes an answer from ChatGPT / Claude / Gemini / a web page and wants it checked
- User shares a URL or a file (report, brief, README, blog draft) and asks how much of it holds up
- **Self-check:** you are about to deliver an answer containing dates, numbers, names, or "X is Y" facts.
  Run `verify` on your own draft before showing it. Fix or caveat anything contradicted or unverified.
- Any decision where a wrong fact is costly (publishing, compliance, money, health, legal, hiring)

## Prerequisites

Register the MCP server once (adds a `verify` tool to Claude Desktop, Claude Code, Cursor, Windsurf, Codex, Gemini CLI):

```bash
npx verify-anything setup
```

Or add it by hand:

```json
{
  "mcpServers": {
    "verify": {
      "command": "npx",
      "args": ["-y", "verify-anything", "mcp"],
      "env": { "LASTSEARCH_API_KEY": "ls_xxx" }
    }
  }
}
```

Keyless use is rate-limited. Free key at [lastsearch.ai](https://lastsearch.ai).

## Tool

```
verify({
  text: "<claim, paragraph, or pasted answer>",   // or
  url: "https://...",                              // a page to fact-check
  depth: "thorough",      // default. "fast" for quick triage (weaker verifier)
  maxClaims: 20
})
```

From a shell, the same thing:

```bash
npx verify-anything "<text>"            # text
npx verify-anything https://...         # a page
npx verify-anything report.md           # a file
pbpaste | npx verify-anything           # the clipboard (a pasted AI answer)
npx verify-anything ... --verbose       # every source weighed, with scores
npx verify-anything ... --json          # machine-readable; exit code 1 if anything is contradicted
```

## Response Shape

```json
{
  "summary": { "totalClaims": 5, "verified": 3, "contradicted": 2, "unverified": 0, "grade": "C" },
  "claims": [
    {
      "text": "The Eiffel Tower is located in Berlin.",
      "status": "contradicted",
      "confidence": 0.1,
      "reason": "Evidence contradicts the claim (en.wikipedia.org +1)",
      "evidence": {
        "url": "https://en.wikipedia.org/wiki/Eiffel_Tower",
        "span": "The Eiffel Tower is a lattice tower on the Champ de Mars in Paris, France.",
        "entailment": 0.0, "contradiction": 1.0
      },
      "checks": [ { "domain": "en.wikipedia.org", "span": "...", "entailment": 0.0, "contradiction": 1.0 } ]
    }
  ]
}
```

- `evidence.span` is the exact sentence the verifier judged. Quote it when you report a verdict.
- `checks` lists every source weighed, including dissenting ones. A reason like
  "Supported by 2 sources; 1 dissents (example.com)" means a real disagreement exists — say so.

## How to Report Results

1. Lead with contradictions. Quote the claim, the verdict, the evidence span, and the source URL.
2. Then unverified claims: "no source directly supports this" is not "false" — say exactly that.
3. Verified claims: one line each with confidence and source.
4. Never upgrade an `unverified` to "true" or a `contradicted` to "nuanced" on your own knowledge.
   If you disagree with the verifier, say so explicitly and show both.
5. When self-checking your own draft, fix contradicted facts, add caveats to unverified ones, and
   tell the user the draft was verified and what changed.

## Interpreting Confidence

Confidence is computed from evidence (entailment strength, number of independent corroborating
domains, dissent), not from a model's self-assessment. It is an estimate, not a guarantee:
`thorough` (default) uses the stronger verifier and is right more often on dates and numbers; `fast` is
triage. For anything critical, open the source.

## Example

User: *"ChatGPT told me Python 4 came out in 2024 — true?"*

```
verify({ text: "The latest major version, Python 4, was released in 2024." })
```

Report: **Contradicted** (python.org, Wikipedia, builtin.com): "Python 3.15.0, released on 9 October 2026"
and "Python 4.0 will probably never come — according to the creator". The current major version is 3.x.
