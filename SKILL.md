---
name: opportunity-confidence
description: 'Produces a simple no-scroll 16:9 HTML opportunity confidence slide for Shopify B2B/Plus sales opportunities. Scores Commercial Fit, Technical Fit, Momentum, and Launch Confidence as equally weighted dimensions, then returns one of four outputs: Proceed, Hold, Need Information, or Potential DQ. Use when the user asks about deal disqualification, DQ triage, opportunity confidence, whether to continue a deal, swipe-left analysis, or AI-assisted opportunity review for Shopify B2B sales. Works in Codex, Claude, Cursor, and Pi.'
---

# Opportunity Confidence

Create a **single self-contained HTML slide** that helps an AE/SE decide whether a Shopify B2B/Plus opportunity should be marked Proceed, Hold, Need Information, or Potential DQ. The output is decision support, not an automatic CRM action.

## Default Output

Unless the user explicitly asks for only text, produce an HTML file using `assets/opportunity-confidence-slide.html` as the visual/template contract:

- One 16:9 slide only.
- No scrolling required at 1366x768 or larger.
- Dark Shopify-adjacent visual style.
- Clean professional typography:
  - `Opportunity Confidence Report` persistent top title uses `Circus Display`.
  - Merchant / opportunity name uses `Chlakh`.
  - All remaining text uses `Geist`.
- Header content:
  - `OPPORTUNITY CONFIDENCE REPORT`
  - Merchant / opportunity name
  - one-line opportunity summary
  - compact metadata line: account, stage, opp id when known, AE, SE
- A small top-right radar graphic that fits the existing empty header space and does not move other layout elements.
- Main score panel:
  - large overall percentage
  - status label
  - recommendation headline
  - short deal consensus paragraph
  - no visible score calculation or weighted formula line
- Four scorecards:
  - Commercial Fit, equal weight
  - Technical Fit, equal weight
  - Momentum, equal weight
  - Launch Confidence, equal weight
  - each card includes a slim progress bar matching that card's score
- A small **Details** button under each score that expands the concise reasoning behind that score.
- Bottom synopsis panel: one dense paragraph with the most important opportunity facts, risks, and next action.
- If recommendation is **Potential DQ**, the synopsis card must include an **Additional info** button that opens a popup with the possible remedies and recheck criteria.

Do not add extra sections below the slide. If you need more detail, write a short Markdown summary in the chat, not in the HTML.

## Inputs

Ask only if missing:

1. Merchant/account name or opportunity link.
2. Optional source preference: Salesforce, SE-NTRAL, pasted notes, or "use what exists".

If the user provides only a short paragraph, proceed with a clear evidence caveat and label assumptions.

## Data Gathering

Use available sources; parallelize where possible.

| Source | Use for |
| --- | --- |
| Salesforce | Stage, amount, close date, `NextStep`, owner/team, record type, products, tasks/events, win/loss history. |
| SE-NTRAL | Discovery, technical assessment, briefings, solution notes, launch constraints. |
| Buyer communications | Gmail, Salesloft, call transcripts, calendar, meeting notes, Slack deal context, and user-provided messages for persona buy-in, objections, blockers, and next-step ownership. |
| User paste/chat | Call notes, email snippets, Slack excerpts; tag as user-provided. |
| Revenue/SOQL MCP | Use when available; if blocked, state data gap and confidence caveat. |

Never fabricate amounts, names, dates, owners, GMV, or stage. Mark unknowns as `unknown`.

## Source Discovery Guide

Use the bundled source-discovery guide `references/opportunity-source-inventory.md` to find the evidence needed to accurately score opportunities. It is a portable summary of this Google Doc:

```text
https://docs.google.com/document/d/1yTzPgKcqi3lJBmDPwzj9D5wqIpsb1iY8nlcXVY80KUM/edit?tab=t.0#heading=h.pgi4yq1gml45
```

Read the bundled reference first. If Google Workspace tools are available, also read the live doc before scoring and use it to catch any updates since the bundled copy was created. If the doc is inaccessible, state that gap and proceed with the bundled source guide, Salesforce, SE-NTRAL, local merchant files, and user-provided context. Do not guess that a source exists just because the guide mentions it.

## Scoring Model

Score each dimension from 0-100.

| Dimension | High Score Means | Low Score Means |
| --- | --- | --- |
| Commercial Fit | Funded, clear pain, executive/champion access, meaningful GMV/revenue, realistic procurement path. | No budget, weak pain, no economic buyer, stalled procurement, low commercial value. |
| Technical Fit | Shopify B2B/Plus capabilities align; integrations and data flows have a credible implementation path. | Hard platform gap, unscoped integration, unsupported requirement, or custom work that breaks timeline/value. |
| Momentum | Recent progress, live stakeholder engagement, next steps scheduled, and timeline urgency. | Ghosting, repeated slips, no next step, stale activities, or only vendor-side chasing. |
| Launch Confidence | Timeline, implementation owner, partner/SOW, data migration, launch window, and change management look achievable. | Launch date is aspirational, owner unclear, partner/SOW missing, dependencies unmanaged, or timeline conflicts with merchant planning. |

## Buyer Persona / Communications Analysis

Review all available buyer communications before final scoring when tools or user-provided artifacts are available. Use Gmail, Salesloft, call transcripts, meeting notes, calendar artifacts, Slack deal context, Salesforce activities/emails, and pasted messages to identify the buyer personas involved and whether each persona is creating buy-in, neutrality, risk, or active blocking.

Classify every material buyer-side participant into one or more personas when evidence supports it:

- Economic buyer / executive sponsor
- Champion or internal seller
- Technical buyer / architect / IT owner
- Implementation owner / operations owner
- Procurement / legal / security reviewer
- End-user or business stakeholder
- Blocker / detractor

Use persona evidence to adjust the score:

- Increase **Commercial Fit** and **Momentum** when the economic buyer or executive sponsor is engaged, pain is stated in their words, a champion is driving internal alignment, or buyer-side stakeholders own next steps.
- Increase **Technical Fit** and **Launch Confidence** when technical or implementation owners participate constructively, accept ownership of integrations/data/workback plans, and unblock scope questions.
- Decrease **Commercial Fit** and **Momentum** when there is no economic buyer, no champion, stakeholder silence, only seller-side chasing, vague interest without owner action, or procurement/legal/security is stalled.
- Decrease **Technical Fit** and **Launch Confidence** when technical, implementation, security, legal, or procurement personas are active blockers, reject required scope, cannot name owners, or expose launch-critical gaps.
- Use **Need Information** and cap the score at 59 when stakeholder identity, buyer-side buy-in, or communications evidence is missing and those gaps could materially change the recommendation.
- Use **Potential DQ** when buyer communications show no buyer-side buy-in, an active blocker with decision power, executive/economic-buyer rejection, an unsuitable use case, or unresolved commercial/technical objections that make the deal unlikely. The synopsis must explain the persona-based DQ reason and state whether a realistic action could resolve it.

Do not over-score based on positive internal seller sentiment. Buyer-side actions, commitments, replies, and owned next steps carry more weight than seller interpretation. Mention the most important persona signal in the relevant scorecard Details text and in the synopsis when it changes the recommendation.

## Launch Confidence / Time-to-Value Rules

Derive Launch Confidence from the bundled reference `references/launch-confidence-time-to-value.md`. This reference is copied from `/Users/terrykealey/Downloads/launch-dq-peer-handoff-all-context.md` so the skill remains portable. Read the relevant sections before scoring Launch Confidence, especially:

- Executive summary
- Data / input model
- Launch definitions to keep separate
- Model mechanics
- Risk signals, blockers, and credits
- Discovery questions and missing-input behavior
- Qualitative failure archetypes

Core launch principles to apply:

- Predict **time to first measurable value**, not plan conversion, store existence, detected launch, or technical go-live.
- Product Fit, Launch Capacity, and Time to Value are separate axes.
- Native paid activity beats admin activity. Draft orders, imports, test activity, sandbox activity, and partner-only progress do not prove first value.
- Missing launch context should trigger Need Information or lower Launch Confidence, not overconfident DQ.
- Strong commercial intent plus a solvable technical path should avoid false disqualification.
- Target dates can be floors. If the merchant has a credible sequenced plan, do not predict faster than their plan without evidence.
- Preserve original committed/proposed launch date separately from current expected launch date; repeated estimate changes lower Launch Confidence.

Launch Confidence should consider:

| Signal | Raises Score | Lowers Score |
| --- | --- | --- |
| Launch owner model | Named merchant-side owner, partner/PMO/Launch role accepted. | No implementation owner, no partner, single overloaded stakeholder. |
| VTP / MAP / launch plan | Clear MVP, first-value metric, target date, acceptance path. | No VTP/MAP for complex B2B, vague launch intent, no first-value metric. |
| Dependency control | ERP/middleware owner, SOW, data migration, buyer onboarding, roadmap dependencies are scoped. | Unknown ERP owner, partner delay, build/test delay, PMF 4 launch-critical blocker. |
| First-value quality | First paid/native B2B orders or named buyer cohort adoption defined. | Imports, drafts, test orders, or technical go-live treated as value. |
| Timeline evidence | Credible external event, realistic target launch date, few estimate changes. | Re-baselined date, stale launch case, merchant silence, no response after burndown. |

If a launch-killer pattern fires, do not hide it inside the score. Name it in the Launch Confidence Details text and make the recommendation Need Information, Hold, or Potential DQ as appropriate.

Overall score:

```text
overall = round((commercial + technical + momentum + launch_confidence) / 4)
```

Use the formula internally only. Do **not** show the calculation in the HTML output. Text such as `Overall = (82 + 58 + 80 + 64) / 4 = 71%` must not appear in real runs.

## Recommendation Outputs

The recommendation must use exactly one of these four outputs:

| Overall / Condition | Recommendation | Status Label |
| --- | --- | --- |
| 70-100 | Proceed | Strong - proceed with confidence |
| 60-69 | Hold | Mixed - hold pending proof |
| Missing sources or critical information gaps; score must be no higher than 59 | Need Information | Incomplete - more evidence required |
| Below 50, or any massive technical/commercial blocker | Potential DQ | High DQ risk |

Use **Need Information** when source coverage is insufficient or critical facts are missing, especially if the missing facts could change the recommendation. In this case, cap the overall score at 59 even if available evidence looks promising, and name the missing evidence clearly in the synopsis and chat response.

Use **Potential DQ** when the overall score is below 50, or when there is a severe blocker that should override the average score. Severe blockers include an unsuitable use case, active launch-critical technical blockers, unsolved platform fit issues, no buyer-side buy-in, no economic buyer/champion, no budget/procurement path, or buyer-side inactivity that indicates the opportunity is not real. For every Potential DQ output, the synopsis and chat response must state the primary DQ reason and whether anything can be done to resolve it, such as executive alignment, technical validation, partner scope, procurement path, buyer-side owner assignment, or use-case re-scoping. If no realistic resolution exists, say that directly.

If a bundled reference uses older recommendation terms, map them to the current output model: `Proceed with plan` or `Stay current plan` usually maps to **Proceed** or **Hold** based on score; `Validate first` maps to **Need Information**; `Disqualify` maps to **Potential DQ**.

Before recommending Potential DQ, always run a false-DQ check:

- Is there executive sponsorship or an engaged champion?
- Is the account strategically important or revenue meaningful?
- Is the technical blocker solvable with partner/custom work?
- Is the issue only timing, resourcing, or missing discovery?
- Is there new evidence that contradicts a historical DQ pattern?

If any answer is strong, prefer Need Information or Hold and name the last-verification criteria.

For **Potential DQ**, do not stop at the label. Include:

- Primary reason: the commercial, technical, launch, momentum, or persona blocker that triggered Potential DQ.
- Remediation path: the specific action that would change the recommendation, or `No realistic remediation identified` if the blocker appears structural.
- Recheck criteria: the minimum evidence needed to move the opportunity back to Need Information, Hold, or Proceed.

For **Potential DQ** HTML output, put the remediation path and recheck criteria in the synopsis card behind a popup control. Replace `{{ADDITIONAL_INFO_SECTION}}` with this exact structure, keeping the text concise enough to fit:

```html
<details class="additional-info">
  <summary>Additional info</summary>
  <div class="additional-info-panel">Primary reason: ... Remedy: ... Recheck criteria: ...</div>
</details>
```

For **Proceed**, **Hold**, and **Need Information**, replace `{{ADDITIONAL_INFO_SECTION}}` with an empty string.

## HTML File Rules

1. Save to `/Users/terrykealey/Opportunity Confidence Reports/<slug-or-opp-id>/opportunity-confidence-slide.html`.
2. Create the folder if missing.
3. If a matching SE-NTRAL merchant folder exists, also mirror the HTML there.
4. Use a compact 16:9 layout. No vertical scroll.
5. Replace every template placeholder. No `{{PLACEHOLDER}}` strings may remain.
6. Use exactly four equal scorecards: **Commercial Fit**, **Technical Fit**, **Momentum**, and **Launch Confidence**.
7. Populate every card's details placeholder:
   - `{{COMMERCIAL_DETAILS}}`
   - `{{TECHNICAL_DETAILS}}`
   - `{{MOMENTUM_DETAILS}}`
   - `{{LAUNCH_CONFIDENCE_DETAILS}}`
8. Populate `{{ADDITIONAL_INFO_SECTION}}`:
   - Potential DQ: a details popup with summary text exactly `Additional info`.
   - Other recommendations: empty string.
9. Populate the radar placeholders from the four score values. Use a 500x500 SVG coordinate system with center `250,250` and radius scale `1.8`:
   - `{{RADAR_COMMERCIAL_Y}}` = `round(250 - commercial * 1.8)`
   - `{{RADAR_TECHNICAL_X}}` = `round(250 + technical * 1.8)`
   - `{{RADAR_LAUNCH_Y}}` = `round(250 + launch_confidence * 1.8)`
   - `{{RADAR_MOMENTUM_X}}` = `round(250 - momentum * 1.8)`
   - `{{RADAR_POINTS}}` = `250,RADAR_COMMERCIAL_Y RADAR_TECHNICAL_X,250 250,RADAR_LAUNCH_Y RADAR_MOMENTUM_X,250`
10. Keep all content short enough to fit the slide:
   - deal consensus: 2-3 sentences max
   - synopsis: 80-120 words max; if recommendation is Potential DQ, include the DQ reason and remediation path
   - Additional info popup for Potential DQ: 45-75 words max
   - each details paragraph: 35-55 words max
   - no bullets in the slide body unless the user asks

## Chat Response

After writing the HTML file, respond with:

- File path(s) created.
- Overall score and recommendation.
- Top 2 reasons.
- For Potential DQ, the DQ reason, whether it can be resolved, and the recheck criteria.
- Missing evidence or assumptions that could change the score.

Keep the chat response concise; the HTML slide is the deliverable.

## Guardrails

- Do not change Salesforce stages or post to Slack unless explicitly asked.
- Do not present the score as a quota or automatic DQ mandate.
- If discrimination, HR, or protected-class concerns are the basis for re-evaluation, do not score; refer to People/Legal.
- When evidence is thin, say so in the synopsis or chat response rather than pretending certainty.

## Trigger Examples

- "Should we DQ [merchant]?"
- "Opportunity confidence for [merchant]"
- "Swipe left analysis"
- "DQ triage for this thread"
- "Create a confidence report"
- "Should we keep pursuing this B2B deal?"

Read this skill in full at the start of a B2B DQ/opportunity-confidence request.
