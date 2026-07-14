# Labeling Styles by Deliverable Type

The three-tier system (Verified / Estimated / Unknown) stays constant — only its visual
presentation changes by document type. Pick the style that fits the deliverable so labels read
as professional, not like debug output.

## Working drafts / internal scratch (shown inline, informal)

Use bracket tags directly in the text while drafting:

```
Revenue grew 12% YoY [VERIFIED: Q3 financials.xlsx, cell B14].
Estimated addressable market is $40-60M [ESTIMATED: based on stated user base x avg
industry ARPU — not from a market report].
Current NPS: [NEEDS SOURCE: not provided in this conversation]
```

Strip or convert these before final delivery — they're for your own tracking, not typically
shown verbatim to the user unless they asked for a working/annotated draft.

## Polished reports, briefs, memos (external-facing)

Use footnote-style or parenthetical citations that read naturally, plus the Sourcing Note at
the end. Don't use the word "VERIFIED" in the visible prose — it reads like a QA artifact, not
a professional document.

```
Revenue grew 12% YoY.¹
Based on comparable SaaS pricing models, the addressable market is roughly $40-60M —
this is a directional estimate, not a published figure.
Current NPS was not available at time of writing.

¹ Source: Q3 financials provided by user.
```

Estimates get flagged inline with plain language ("roughly," "directional estimate," "not a
published figure") rather than a bracket tag. Unknowns get written as a plain sentence stating
the gap, not silently dropped.

## Stakeholder / exec updates (short-form)

No footnotes, no tables. One inline qualifier per estimated/unknown claim, and a single closing
line if there's more than one caveat:

```
Churn held steady at 4.2% this quarter (per Q3 dashboard export).
We think the new onboarding flow is driving most of the retention gain, though we don't
have attribution data yet to confirm that.
```

## Data tables / spreadsheets

Add a status column or use cell comments/notes rather than inline text:

| Metric | Value | Status |
|---|---|---|
| Q3 Revenue | $1.2M | Verified (financials.xlsx) |
| Market size | $40-60M | Estimated |
| Current NPS | — | Needs input |

## Citations that name a specific external source

Only ever write "According to [X]," "[X] reports," or similar phrasing when [X] is a source you
actually retrieved via a tool in this conversation and can point to the specific passage that
supports the claim. If you can't do that, do not name a source — write the claim as your own
estimate instead, or flag it as unknown. A generic-sounding real-source-shaped citation attached
to an unverified number is the single most damaging failure mode this skill exists to prevent —
it's more misleading than an unsourced number, because it borrows credibility that wasn't earned.
