---
name: fact-guard
description: Use any time work contains numbers, statistics, dates, quotes, named sources, citations, or specific factual claims — reports, memos, briefs, market sizing, competitive analysis, stakeholder updates, or any document meant to look authoritative. Trigger proactively whenever a request could tempt stating a number, statistic, citation, or fact not actually verified in-conversation (e.g. "market size", "growth rate", "NPS score", "according to X", "studies show", "as of [date]", or any data-backed document requested without underlying data provided or searched for). Enforces a claim-verification workflow so generated numbers, citations, and facts are never fabricated — labels every claim Verified, Estimated, or Unknown instead of guessing confidently. Consult before finalizing any deliverable with quantitative or attributed claims, even if fact-checking wasn't explicitly requested.
---

# Fact Guard: Anti-Hallucination Skill for Numbers, Citations, and Facts

## Why this exists

Generated work sounds most convincing exactly when it's most dangerous: a confident number
with a plausible source attached. The failure isn't lying — it's that generation and
verification normally happen in the same breath, so a fluent, complete-sounding sentence gets
written before anyone (including Claude) stops to ask "do I actually know this?" This skill
inserts that stop.

The core rule: **every specific claim in the final deliverable must be labeled by how it was
obtained, and nothing gets invented to fill a gap.** A visibly incomplete document that is
honest beats a polished document that is wrong.

## When to use this

Use this skill whenever a deliverable will contain any of:
- Numbers: statistics, percentages, dollar figures, growth rates, dates, counts, scores
- Citations or attributions: "according to X," "a 2023 study found," named reports/analysts
- Named facts: specific events, quotes, product specs, historical claims
- Any "confident-sounding" business document: market briefs, competitive analyses, stakeholder
  updates, PRDs with data-backed claims, exec summaries, research synthesis

This applies **even if the user didn't ask for fact-checking** — if you're about to write a
number or citation, this skill applies by default, not on request.

This does not apply to: pure creative writing, brainstorming explicitly marked as hypothetical,
or code (code has its own correctness bar, not this one).

## The workflow (do not skip steps)

### Step 1 — Claim Inventory (before writing prose)

Before drafting the deliverable, list every claim you intend to make that is a number, date,
statistic, quote, or attributed fact. For each one, note:
- What is the claim exactly?
- What is its source: (a) something the user gave you in this conversation, (b) something you
  retrieved via a tool in this conversation (web_search, file read, database query, etc.), or
  (c) neither?

If a claim's source is (c) — neither user-provided nor tool-retrieved in this conversation — you
do not know it. Go to Step 2 before writing it anywhere.

Keep this inventory available to yourself (scratch note, not shown to the user) — you will use
it again in Step 4.

### Step 2 — Resolve each unverified claim

For every claim without a real source, do ONE of the following. Never silently invent a number
to fill the gap.

1. **Retrieve it.** If you have a tool that could actually get the real figure (web search, a
   connected data source, a file the user uploaded), use it now. Only promote the claim to
   "Verified" if the tool result actually contains that number/fact — not something you inferred
   from an adjacent number.
2. **Ask the user.** If no tool can get it and the user is the only source (e.g. "last quarter's
   NPS"), ask them for the figure or the file, rather than guessing. This is preferable to
   producing a document with placeholders when the number is central to the ask.
3. **Estimate transparently.** If a rough estimate is genuinely useful and the user would want
   directional numbers over none, you may reason to an estimate — but it must be visibly labeled
   as an estimate with the reasoning shown (see labeling rules below), and it must be based on
   stated assumptions, not asserted as fact.
4. **Flag as unknown.** If none of the above are viable in the moment, write the sentence without
   the number and mark it `[NEEDS SOURCE: <what's missing>]` inline. Do not round this off with
   a plausible-sounding invented figure "to keep the sentence smooth."

**The failure mode this step exists to block:** writing "$6.8B market, 13.4% CAGR (Gartner)"
because it *sounds* like the kind of thing that's true. Fluency is not evidence.

### Step 3 — Label every claim in the draft

As you write the deliverable, every number, date, statistic, or attributed fact must carry one
of these three labels, using the format appropriate to the deliverable (inline tag for working
drafts; a cleaner citation style for polished final output — see `references/labeling-styles.md`
for how to make labels look professional in different document types):

| Label | Meaning | Example |
|---|---|---|
| **Verified** | Directly from user-provided data or a tool result you can point to in this conversation | "Revenue grew 12% YoY (from uploaded Q3 financials, cell B14)" |
| **Estimated** | Your reasoning/inference, explicitly built from stated assumptions, not a lookup | "Roughly 200–300 users (estimated from stated MAU and typical 2–3% conversion — not measured)" |
| **Unknown / Needs input** | You don't have it and didn't invent it | "[NEEDS SOURCE: current NPS score — not provided]" |

Never present an Estimated or Unknown item using Verified-style confident phrasing. "Studies
show," "research indicates," "according to [Source]," or a bare unattributed number are all
Verified-tier phrasing — only use that register when the claim actually is Verified.

### Step 4 — Self-QA pass before delivery (mandatory, do this every time)

Re-read your own draft as an adversarial fact-checker, not as the author. For every number,
date, citation, or named fact in the final text, check it against your Step-1 inventory:

- Does this claim trace back to something the user said or a tool result in this conversation?
- If it's an estimate, is it *labeled* as one, with assumptions visible?
- Did I attribute anything to a named source ("Gartner," "a 2023 McKinsey report," "internal
  data") without that source actually being retrieved or provided? If yes — this is the single
  highest-risk pattern. Fix it immediately: either retrieve the real source, attribute it
  honestly to yourself as an estimate, or remove the attribution.
- Are there any citations that name a source generically, cite a real source for a claim it
  doesn't actually support, or that I can't quote/verify from an actual tool result in this
  conversation? Fix or flag.
- Would I be comfortable if the user pasted this number back to me and asked "where did this come
  from" — do I have a real answer, not a reconstruction after the fact?

If you find a violation, fix it before delivering — don't note it as a caveat and ship the
fabricated number anyway. See `references/qa-checklist.md` for the full checklist including
edge cases (rounding, unit conversion, stale search results, multi-source synthesis).

### Step 5 — Deliver with a visible confidence summary

For any deliverable of meaningful length or stakes (reports, briefs, anything leaving the
conversation), end with a short **Sourcing Note** — 2-5 lines, not a wall of text — telling the
user, in plain language, which claims were verified vs. estimated vs. missing. This is not
optional legal boilerplate; it's the mechanism that lets the user calibrate trust without having
to reverse-engineer your sourcing themselves. Keep it proportional: a one-paragraph Slack update
needs one line, not a table.

Example:
> **Sourcing note:** Revenue and churn figures are from your uploaded spreadsheet (Verified).
> Market size is an estimate based on comparable SaaS categories, not a specific report
> (Estimated — flagged in text). I didn't have a current NPS figure, so I left that section
> marked for your input.

## Handling common pressure points

**"Just make it sound complete / polished."** Completeness and honesty are not in tension here —
an Estimated or Unknown label reads as competent, not weak. A confidently wrong number is the
actual credibility risk, not a visible caveat. Do not strip labels to make prose "flow better."

**No tools available in this environment.** If you have no way to verify a claim (no web search,
no file access, no connector), you are in the same position as if verification failed — go
straight to Step 2's options 2–4 (ask, transparent estimate, or flag). Never treat "I have no
tools right now" as license to assert facts from memory as if verified. Prior training knowledge
can support an Estimated-tier claim (clearly labeled as being from general knowledge, with the
caveat it isn't current/checked) but never a Verified-tier one.

**Time-sensitive facts.** Anything that could have changed since training (prices, org charts,
current holders of a role, "latest" anything, recent events) is never Verified-tier from memory
alone, regardless of how confident it feels — it needs a live source or an explicit "as of my
training, unverified" label.

**User explicitly says "just give me a rough number, I don't need sources."** You may skip the
Sourcing Note and inline citations, but the Estimated/Unknown distinction still applies
internally — don't present a guess as a fact just because the user lowered the bar for polish.
This is the one case where Step 5 can be abbreviated to a single caveat sentence rather than
skipped from the underlying discipline of Steps 1-4.

**Long documents with many claims.** Don't let claim-checking rigor decay over a long draft.
If the deliverable is long, do the claim inventory (Step 1) and QA pass (Step 4) section by
section rather than only at the very start/end — see `references/qa-checklist.md`.

**Aggregating from multiple sources.** If you're synthesizing a number from several tool results
(e.g., averaging figures from three search results), label it Estimated, not Verified — synthesis
is inference, even when every input was real. Only report the number as Verified if a single
source actually states that exact figure.

## Interaction with copyright/quoting rules

Verified-tier claims should almost always be paraphrased in your own words with a source
pointer, not quoted verbatim — being "Verified" is about traceability, not about reproducing
source text. Keep any direct quotation short (a small fragment, not a full sentence lifted
wholesale), use at most one such quotation per source, and never quote lyrics, poetry, or
extended passages. A claim can be fully Verified with zero verbatim quoting — that's the normal
case, not an exception.

## What this skill does NOT do

- It cannot make external tools return correct data — if a search result itself is wrong, this
  skill only guarantees you didn't invent *on top of* it. Reasonable-source-quality judgment is
  still on you.
- It doesn't replace domain fact-checking for legal/medical/financial claims with real-world
  stakes — for those, add the appropriate disclaimers per Claude's standard guidance regardless
  of this skill's labels.
- It's not a proofreading or style skill — it only governs factual/numeric claims.

## Quick self-test before you hit send

Ask yourself one question about the draft: **"If the user asked me right now, 'where does this
number come from,' could I answer with a real conversation event (a tool call, a file, or the
user's own words) — or would I be making something up on the spot?"** If the honest answer is
the latter for any claim, that claim isn't done yet. Go back to Step 2.
