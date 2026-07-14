# Fact Guard

A Claude Skill that stops hallucinated numbers, citations, and facts from making it into generated work — reports, briefs, stakeholder updates, and anything else that's supposed to look authoritative.

## The problem

Generated text sounds most convincing exactly when it's most dangerous: a confident number with a plausible-looking source attached. That's not because the model is lying — it's that generation and verification normally happen in the same breath. A fluent, complete-sounding sentence gets written before anyone stops to ask "do I actually know this?"

Two real failure patterns this skill targets:

- **The invented statistic.** Asked for a market brief with no data provided or retrieved, a model writes: "The global PM software market was valued at $6.8B in 2023, growing at 13.4% CAGR (Gartner)." No search happened. The number and the citation are both fabricated — but they look exactly like a real one would.
- **The invented trend.** Asked to summarize "last quarter's NPS" with no underlying data, a model writes: "NPS improved from 32 to 41 QoQ, driven by onboarding improvements shipped in March." The numbers and the causal story are both invented to make the paragraph read as complete.

Both are hard to catch precisely because the output is fluent and specific — specificity reads as rigor, even when there's nothing behind it.

## How it works

Fact Guard enforces a workflow rather than a single instruction. It inserts a verification step between "the model wants to write a claim" and "the claim ends up in the document":

1. **Claim inventory** — before drafting, list every number, date, statistic, quote, or attributed fact that's about to be stated, and where it actually comes from.
2. **Resolve unverified claims** — for anything without a real source: retrieve it (search / file / connector), ask the user, estimate transparently with visible assumptions, or flag it as `[NEEDS SOURCE]`. Never fill the gap with a plausible guess.
3. **Label every claim** — every factual statement in the draft carries one of three tiers:
   - **Verified** — traceable to user-provided data or a tool result from this conversation
   - **Estimated** — explicit reasoning/inference, clearly hedged, not presented as fact
   - **Unknown** — flagged, not invented
4. **Adversarial self-QA pass** — before delivery, the model re-reads its own draft as a skeptical fact-checker, specifically hunting for the failure patterns above (invented statistics, borrowed-credibility citations, stale facts presented as current, multi-source blends presented as single-source).
5. **Sourcing note** — the final deliverable ends with a short, plain-language summary of what was verified, estimated, or missing, so the reader can calibrate trust without reverse-engineering the sourcing themselves.

The rule underneath all five steps: **a visibly incomplete document that's honest beats a polished document that's wrong.**

## Usage — important scope note

Fact Guard's trigger behavior differs by task shape, and it's worth understanding this upfront rather than being surprised by it:

**Auto-triggers reliably on:** formal, multi-part deliverables — market briefs, competitive analyses, stakeholder updates, research summaries, anything with several claims that reads like a document. Claude consults skills when a task genuinely needs extra instructions to do well, and a formal deliverable qualifies.

**Needs explicit invocation (`/fact-guard`) for:** quick one-line lookups — a single stock price, a sports score, an exchange rate, "who's the current CEO of X." This is a known constraint of how Claude Skills work, not a bug specific to this skill: Claude only consults a skill when it judges the task can't be done well without it, and a simple factual lookup is something Claude can already attempt unaided — so the trigger can reasonably skip loading the skill even when the topic matches the description closely. No amount of description-tuning fully closes this gap; it's an architectural property of the trigger mechanism, confirmed through direct testing (a description explicitly naming "stock prices," "sports scores," and "current price" still didn't reliably auto-fire on a one-line price check).

**Practical takeaway:** for quick factual lookups where you want the Verified/Estimated/Unknown discipline applied, invoke it explicitly:

> how many goals were created by each team in july 12th worldcup football match
> `/fact-guard`

Result — labeled, sourced, and scoped to only what was actually confirmed:

| Team | Goals |
|---|---|
| Argentina | 3 |
| Switzerland | 1 |

**Status:** Verified — pulled directly from live match data (status: final), not from memory. This was the only World Cup match played on July 12.

That result is the skill working exactly as designed — a real tool call, an explicit Verified label, and a clear statement of what "the only match that day" claim was checked against, rather than an unqualified answer. The only gap is that it needed the explicit `/fact-guard` call to get there; it doesn't yet fire on its own for a request this short.

If your workflow needs quick lookups to be covered without remembering to invoke it, two options that scale better than manual invocation per-message:
- **Claude for Team/Enterprise:** admins can set default instructions at the workspace level, applying the same Verified/Estimated discipline to every user automatically, with no per-user setup.
- **Personal use:** a saved custom instruction in your own Settings → Profile → Preferences can apply similar discipline to quick lookups specifically, though this only covers your own account, not other users of a shared public skill.

## What it handles

- Documents built with real data (labels claims Verified without unnecessary hedging)
- Documents where no data or search results are available (forces asking, estimating transparently, or flagging — never inventing)
- Requests to skip formal sourcing ("just give me a rough number") — internal Verified/Estimated discipline still applies, only the visible caveats shrink
- Long documents, where verification rigor is checked section-by-section rather than only at the start/end
- Multi-source synthesis (e.g. averaging several search results) — correctly labeled Estimated, not Verified, since synthesis is inference even when every input was real
- Time-sensitive facts (prices, current role-holders, "latest" anything) — never treated as Verified from memory alone
- Creative writing and fiction — explicitly out of scope, so it won't misfire on invented statistics that are supposed to be invented

## Repository structure

```
.
├── SKILL.md                       # Core skill: triggering conditions + the 5-step workflow
└── references/
    ├── labeling-styles.md         # How Verified/Estimated/Unknown should look across
    │                               # document types (drafts, reports, exec updates, tables)
    └── qa-checklist.md            # Detailed adversarial QA checklist and failure patterns
```

`SKILL.md` is the only file loaded automatically when the skill triggers. The two reference files are pulled in on demand — keeping the core instructions short while still giving the model detailed guidance when it's actually formatting labels or running the QA pass.

## Installation

This repo isn't packaged as a release yet — for now, clone it directly:

```bash
git clone https://github.com/alokgaikwad24/anti-hallucination-guard.git
```

Then point Claude at the `SKILL.md` + `references/` folder, keeping the structure intact:

- **Claude Code / Cowork:** copy the cloned folder into your skills directory (e.g. `~/.claude/skills/fact-guard/`).
- **Claude.ai / Claude Apps:** package the folder into a `.skill` bundle using Anthropic's [skill-creator packaging script](https://github.com/anthropics/skills), then upload it via Settings → Capabilities → Skills.

## Example prompts

Prompts that reliably auto-trigger fact-guard today:
- "Write a competitive brief on the project management software market"
- "Summarize our NPS trend from last quarter"
- "Draft a stakeholder update citing our Q3 metrics"

Prompts that currently need explicit `/fact-guard` invocation:
- "Compare TCS and Infosys current stock price vs yesterday's close"
- "How many goals were created by each team in the July 12th World Cup football match"
- "What's the current USD to INR exchange rate?"

## Limitations

- It cannot verify that a *retrieved* source is itself correct — it only guarantees the model doesn't fabricate on top of what was retrieved.
- It's not a substitute for domain-specific fact-checking on legal, medical, or financial claims with real-world stakes.
- It governs factual/numeric claims only — not general writing quality, tone, or style.
- **Auto-triggering is unreliable for short, single-fact lookups** (see Usage above). This is a property of how Claude Skills decide when to consult a skill, not something fixable purely through description wording — treat explicit invocation as the expected pattern for quick lookups until Claude's triggering mechanism changes.

## Contributing

Issues and pull requests are welcome — particularly additional test scenarios (positive or negative) that reveal gaps in the labeling or QA logic. If you're proposing a change to `SKILL.md`, please include the scenario that motivated it. If you find a task shape that should auto-trigger but doesn't, please include it as a reproducible prompt rather than a general description — that's the only way to distinguish an actual description gap from the auto-trigger ceiling described above.
