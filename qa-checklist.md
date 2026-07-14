# QA Checklist for Claim Verification (Step 4 detail)

Use this as the adversarial pass before delivering any deliverable governed by fact-guard.
Go claim-by-claim, not paragraph-by-paragraph — it's easy to skim past a number sitting inside
an otherwise-fine sentence.

## Core checks (run for every claim)

1. **Traceability** — Can I point to the exact tool result, uploaded file, or user message that
   contains this claim? If tracing it requires me to reconstruct or "remember roughly," it's not
   Verified.
2. **Attribution accuracy** — If a source is named, does that source actually say this, or does
   it say something adjacent that I rounded into this claim? (Common failure: source gives a
   range, draft states the midpoint as if it were the reported figure.)
3. **Label match** — Does the visible phrasing match the tier? Confident unhedged phrasing
   ("grew 12%") should only appear for Verified claims. Estimated claims need a visible hedge
   ("roughly," "an estimate," "directionally").
4. **Freshness** — Is this a fact that could have changed since my training data or since the
   source was published (prices, roles, "current," "latest," "as of today")? If so, it needs a
   live source, not memory, regardless of how confident it feels.
5. **Synthesis vs. lookup** — If this number was computed by combining multiple retrieved facts
   (sum, average, rate calculated from two other numbers), it's Estimated/Derived, not Verified,
   even though every input was real.
6. **Silent rounding/unit changes** — Did units, currency, or time periods change between the
   source and the draft (e.g., source says "per month," draft implies "per year")? This creates
   a fabricated-feeling number even from real data.
7. **No orphaned citations** — Every "according to X" or footnote marker resolves to something
   real and specific, not a generic placeholder ("industry reports suggest").

## Failure patterns to specifically hunt for

- **The confident invented statistic** — a specific-looking number (13.4%, $6.8B) with no
  retrieval event behind it. These are the most dangerous because specificity reads as rigor.
- **The borrowed-credibility citation** — attaching a plausible real organization's name
  ("Gartner," "McKinsey," "internal analytics") to a claim that org was never actually consulted
  for in this conversation.
- **The stale-but-labeled-current fact** — correctly retrieved once, but presented as "current"
  when the source is old or the topic moves fast.
- **The smoothed-over gap** — a sentence that originally would have needed a
  `[NEEDS SOURCE]` flag, rewritten in a later revision to remove the flag and just... state a
  number, because it "read better."
- **The multi-source blend presented as single-source** — "Reuters reports X" when X was
  actually pieced together from three different outlets.

## Section-by-section discipline for long documents

For anything over ~1500 words or with more than ~10 factual claims, don't save the QA pass for
the very end — verification quality decays across long drafts because working memory of "which
number came from where" gets fuzzy. Instead:
- Do the claim inventory (Step 1) per section as you draft it, not once at the start.
- Do a mini QA pass at the end of each major section before moving to the next.
- Do one final full-document pass at the end focused only on cross-section consistency (e.g.,
  the same figure quoted two different ways in two sections).

## When you find a violation

Fix it in place before delivering. Acceptable fixes, in order of preference:
1. Retrieve the real figure/source now (if a tool can get it).
2. Rewrite as an explicit, labeled estimate.
3. Remove the specific number/attribution and state the gap plainly.

Not acceptable: leaving the fabricated claim in place with an added disclaimer elsewhere in the
document ("note: some figures are estimates") that doesn't actually tell the user *which* ones.
Blanket disclaimers are not a substitute for per-claim labeling — they let a specific fabrication
hide behind a vague caveat.
