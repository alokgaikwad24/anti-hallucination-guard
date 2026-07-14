# Fact Guard

A Claude Skill that stops hallucinated numbers, citations, and facts from making it into generated work — reports, briefs, stakeholder updates, and anything else that's supposed to look authoritative.

## The problem

Generated text sounds most convincing exactly when it's most dangerous: a confident number with a plausible-looking source attached. That's not because the model is lying — it's that generation and verification normally happen in the same breath. A fluent, complete-sounding sentence gets written before anyone stops to ask "do I actually know this?"

Two real failure patterns this skill targets:

- **The invented statistic.** Asked for a market brief with no data provided or retrieved, a model writes: "The global PM software market was valued at $6.8B in 2023, growing at 13.4% CAGR (Gartner)." No search happened. The number and the citation are both fabricated — but they look exactly like a real one would.
- - **The invented trend.** Asked to summarize "last quarter's NPS" with no underlying data, a model writes: "NPS improved from 32 to 41 QoQ, driven by onboarding improvements shipped in March." The numbers and the causal story are both invented to make the paragraph read as complete.
 
  - Both are hard to catch precisely because the output is fluent and specific — specificity reads as rigor, even when there's nothing behind it.
 
  - ## How it works
 
  - Fact Guard enforces a workflow rather than a single instruction. It inserts a verification step between "the model wants to write a claim" and "the claim ends up in the document":
 
  - 1. **Claim inventory** — before drafting, list every number, date, statistic, quote, or attributed fact that's about to be stated, and where it actually comes from.
    2. 2. **Resolve unverified claims** — for anything without a real source: retrieve it (search / file / connector), ask the user, estimate transparently with visible assumptions, or flag it as `[NEEDS SOURCE]`. Never fill the gap with a plausible guess.
       3. 3. **Label every claim** — every factual statement in the draft carries one of three tiers:
          4.    - **Verified** — traceable to user-provided data or a tool result from this conversation
                -    - **Estimated** — explicit reasoning/inference, clearly hedged, not presented as fact
                     -    - **Unknown** — flagged, not invented
                          - 4. **Adversarial self-QA pass** — before delivery, the model re-reads its own draft as a skeptical fact-checker, specifically hunting for the failure patterns above (invented statistics, borrowed-credibility citations, stale facts presented as current, multi-source blends presented as single-source).
                            5. 5. **Sourcing note** — the final deliverable ends with a short, plain-language summary of what was verified, estimated, or missing, so the reader can calibrate trust without reverse-engineering the sourcing themselves.
                              
                               6. The rule underneath all five steps: **a visibly incomplete document that's honest beats a polished document that's wrong.**
                              
                               7. ## What it handles
                              
                               8. - Documents built with real data (labels claims Verified without unnecessary hedging)
                                  - - Documents where no data or search results are available (forces asking, estimating transparently, or flagging — never inventing)
                                    - - Requests to skip formal sourcing ("just give me a rough number") — internal Verified/Estimated discipline still applies, only the visible caveats shrink
                                      - - Long documents, where verification rigor is checked section-by-section rather than only at the start/end
                                        - - Multi-source synthesis (e.g. averaging several search results) — correctly labeled Estimated, not Verified, since synthesis is inference even when every input was real
                                          - - Time-sensitive facts (prices, current role-holders, "latest" anything) — never treated as Verified from memory alone
                                            - - Creative writing and fiction — explicitly out of scope, so it won't misfire on invented statistics that are supposed to be invented
                                             
                                              - ## Repository structure
                                             
                                              - ```
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
                                                - - **Claude.ai / Claude Apps:** package the folder into a `.skill` bundle using Anthropic's [skill-creator packaging script](https://github.com/anthropics/skills), then upload it via Settings → Capabilities → Skills.
                                                 
                                                  - ## Usage
                                                 
                                                  - You don't need to invoke this explicitly — it's written to trigger proactively whenever a request could tempt a model into stating a number, statistic, citation, or fact it hasn't actually verified in the conversation (market sizing, growth rates, "according to X," "studies show," data-backed reports without supplied data, etc.).
                                                 
                                                  - Example prompts that should trigger it:
                                                  - - "Write a competitive brief on the project management software market"
                                                    - - "Summarize our NPS trend from last quarter"
                                                      - - "Draft a stakeholder update citing our Q3 metrics"
                                                       
                                                        - ## Limitations
                                                       
                                                        - - It cannot verify that a *retrieved* source is itself correct — it only guarantees the model doesn't fabricate on top of what was retrieved.
                                                          - - It's not a substitute for domain-specific fact-checking on legal, medical, or financial claims with real-world stakes.
                                                            - - It governs factual/numeric claims only — not general writing quality, tone, or style.
                                                             
                                                              - ## Contributing
                                                             
                                                              - Issues and pull requests are welcome — particularly additional test scenarios (positive or negative) that reveal gaps in the labeling or QA logic. If you're proposing a change to `SKILL.md`, please include the scenario that motivated it.
                                                              - 
