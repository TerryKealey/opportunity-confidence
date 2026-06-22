# Opportunity Scoring Skill - Source Inventory and Evidence Map

Source Google Doc:
`https://docs.google.com/document/d/1yTzPgKcqi3lJBmDPwzj9D5wqIpsb1iY8nlcXVY80KUM/edit?tab=t.0#heading=h.pgi4yq1gml45`

## Purpose

This document inventories the sources an opportunity-scoring skill can use to score Shopify sales opportunities across four dimensions:

- Commercial Fit
- Technical Fit
- Momentum
- Launch Confidence

The intent is to combine historical Shopify experiential data with current opportunity-specific context and activity. The best score should be evidence-weighted: current source-of-truth systems first, then merchant-specific notes/transcripts, then historical pattern calibration, then explicit inference where needed.

## Recommended Evidence Priority

1. Salesforce opportunity/account/case data — source of truth for opportunity metadata, ownership, stage, revenue, close date, launch case, products, and pipeline history.
2. SE-NTRAL merchant context — highest-value qualitative source for discovery, technical assessments, PMF assessments, solution notes, and merchant-specific risks.
3. Sales calls/transcripts, email, Slack, and calendar artifacts — best source for live momentum, stakeholder behavior, urgency, objections, and next-step quality.
4. Launch data and store telemetry — best source for Launch Confidence once a merchant has signed or entered launch.
5. Historical Closed Lost / DQ calibration data — pattern-matching layer to avoid repeating known pursuit mistakes.
6. Shopify product and platform documentation — technical feasibility validation, not deal sentiment.
7. External public data — company size, market signals, ecommerce maturity, funding/news, and competitive context.
8. User-provided notes/files — useful but should be tagged as user-provided and reconciled against system data.

## Category-Specific Source Mapping

### Commercial Fit

Primary sources:

- Salesforce Opportunity: stage, revenue, close date, products, forecast, NextStep, competitors, stage age.
- Salesforce Account and Copilot Accounts: GMV/revenue, industry, region, store/location count, product eligibility, existing Shopify footprint.
- Discovery/briefing docs: pain severity, business objectives, economic buyer, decision criteria.
- Contacts/opportunity roles: champion, executive sponsor, technical owner, procurement/legal contacts.
- Quotes/CPQ: package maturity, pricing approval, commercial commitment.
- Public company/news data: growth, funding, strategic ecommerce initiatives, distress signals.

Useful scoring signals:

- Clear funded initiative tied to material revenue or strategic priority.
- Identified economic buyer and engaged champion.
- Product scope matches Shopify's monetizable offerings.
- Procurement path is known and active.
- Merchant size and complexity justify sales/launch investment.

Score down when:

- No economic buyer, no budget, vague pain, low GMV, no clear product fit, locked incumbent contract, or repeated no-decision history.

### Technical Fit

Primary sources:

- SE-NTRAL technical assessment, PMF assessment, discovery docs.
- Sales call transcripts for exact requirements and objections.
- Partner SOW/proposal for implementation approach.
- Shopify dev/help docs and Athena reference for capability validation.
- Existing stack evidence from merchant website, Drive docs, discovery, or public tech-stack data.

Useful scoring signals:

- Requirements map cleanly to Shopify primitives: B2B companies/catalogs, Markets, Checkout Extensibility, Functions, Customer Accounts, POS, Payments, Admin APIs.
- Integration pattern is credible for ERP/OMS/PIM/WMS/tax/payment systems.
- Data migration scope is understood.
- Known gaps have a partner/custom-app path.
- Timeline reflects actual complexity.

Score down when:

- Requirement conflicts with Shopify platform limits, real-time dependency is unscoped, ERP/OMS complexity is unknown, critical customizations are hand-waved, or launch depends on unsupported workflows.

### Momentum

Primary sources:

- Salesforce tasks/events/emails and stage history.
- Salesloft activities, calls, meetings, conversations.
- Gmail search for buyer responsiveness and next steps.
- Calendar meetings and attendee RSVP status.
- Slack deal channel and cross-channel mentions.
- Sales call transcripts and recent notes.

Useful scoring signals:

- Recent buyer-side activity, not just seller follow-up.
- Concrete next meeting scheduled with required stakeholders.
- Champion is driving internal alignment.
- Mutual action plan exists and is current.
- Close date has not repeatedly slipped without new evidence.
- Procurement/legal/security work is actively progressing.

Score down when:

- Ghosting, no next step, stale activities, repeated close-date slips, only AE/SE chasing, missing stakeholders, or "interested but not urgent" language.

### Launch Confidence

Primary sources:

- Salesforce Launch Case and case health/status.
- Launch kickoff materials, launch plan, workback schedule, partner SOW.
- Store/shop telemetry: password status, orders, B2B company count, B2B/DTC GMV progress, shop readiness.
- Technical assessment and implementation dependency list.
- Partner involvement and implementation owner.
- Migration plan: data, integrations, theme, checkout, payments, tax, fulfillment, reporting.

Useful scoring signals:

- Named implementation owner: merchant, partner, or internal launch role.
- Realistic launch date with workback plan.
- Partner/SOW exists where complexity warrants it.
- Data migration and integration owners are assigned.
- Store build progress is measurable.
- Open launch blockers are tracked in Salesforce case or launch plan.

Score down when:

- Launch date is aspirational, no owner, partner not scoped, technical blockers unresolved, data migration not planned, merchant has limited implementation capacity, or post-sale handoff is unclear.

## Historical Calibration Sources

Historical data should not replace current deal evidence, but it should shape the confidence score and recommendation.

Use these historical sources to calibrate:

- AMER/EMEA Closed Lost DQ analyses.
- Existing opportunity-confidence reports.
- Prior PMF assessments.
- Salesforce closed-lost history for the same account or similar accounts.
- Launch inspector historical snapshots.
- Similar merchant SE-NTRAL folders where patterns are comparable.
- Product/platform historical gap notes from public Shopify docs and Athena references.

Pattern examples to detect:

- Timing/unresponsive: promising discovery, then activity decay and close-date slips.
- No budget: engagement without commercial owner or procurement path.
- Technical mismatch: requirement repeatedly framed as mandatory but unsupported or unscoped.
- Launch risk: deal can close, but implementation owner/timeline is weak.
- False-DQ risk: strategic account, real champion, or solvable blocker is present despite weak short-term momentum.

## Evidence Confidence Model

Use a separate evidence confidence label in chat/source audit so the skill does not pretend precision when sources are thin.

High confidence requires at least:

- Salesforce opportunity/account data.
- Recent activity source such as tasks/events/email/calls.
- Merchant-specific discovery or technical assessment.
- Either transcript/verbatim evidence or launch/implementation artifacts for late-stage deals.

Medium confidence usually means:

- Salesforce metadata plus SE-NTRAL/Drive notes.
- Salesforce metadata plus strong recent activity, but missing transcripts or technical assessment.

Low confidence means:

- Opportunity metadata only.
- User-provided summary only.
- Stale docs with no recent activity.
- Major source access failures.

When confidence is low, cap strong recommendations and ask for the missing evidence before recommending DQ or confident pursuit.

## Minimum Source Set by Deal Stage

| Deal Stage | Minimum Sources Before Scoring | Stronger Sources |
| --- | --- | --- |
| Early discovery | Salesforce opp/account, discovery notes or call transcript, known pain/use case | SE-NTRAL briefing, website/tech-stack evidence, stakeholder map |
| Technical validation | Salesforce, technical assessment, call transcript, requirements list | Partner SOW, Shopify docs validation, integration architecture |
| Proposal / negotiation | Salesforce opp + quote, stakeholder map, recent email/activity, technical fit notes | Procurement/legal artifacts, executive sponsor evidence, mutual action plan |
| Commit / closing | Salesforce opp + quote, close plan, recent buyer confirmation, launch owner | Launch case or pre-launch plan, partner SOW, migration plan |
| Closed Won / launch | Launch case, implementation owner, launch plan, shop data, blocker list | Store telemetry, B2B/DTC order progress, launch inspector sync history |

## Suggested Skill Workflow

1. Resolve merchant/opportunity. Search SE-NTRAL/local folders, then Salesforce by merchant name or Opp ID.
2. Gather system-of-record data. Pull Salesforce opportunity, account, team, contacts, products, activities, quote, and cases.
3. Gather qualitative evidence. Read SE-NTRAL docs, Drive docs, PMF/tech assessments, partner SOWs, and local files.
4. Gather live momentum. Pull recent Gmail, calendar, Salesloft, Slack, and transcripts where available.
5. Gather launch evidence. For late-stage/closed-won deals, check Launch Case and store/shop telemetry.
6. Run historical calibration. Compare against DQ patterns and prior reports.
7. Score dimensions. Score Commercial Fit, Technical Fit, Momentum, and Launch Confidence from 0-100.
8. Run false-DQ check. Before recommending disqualification, check for executive sponsorship, strategic value, solvable blockers, and new evidence.
9. Output source audit. Every score should include source tags and missing evidence that could change the score.

## Guardrails

- Never fabricate ARR, GMV, stage, close date, names, owners, transcripts, or technical requirements.
- Do not treat internal sentiment as buyer evidence unless buyer behavior supports it.
- Do not over-score momentum based only on seller activity.
- Do not DQ solely because technical fit is incomplete if a credible partner/custom path exists.
- Do not score Launch Confidence high without an implementation owner and timeline evidence.
- Tag material claims with source family: `[Salesforce]`, `[SE-NTRAL]`, `[Gmail]`, `[Slack]`, `[Salesloft]`, `[Calendar]`, `[BQ]`, `[Partner-SOW]`, `[PMF]`, `[Shopify Docs]`, `[DQ-Calibration]`, `[User-provided]`, or `[Inference]`.
- If sources conflict, state the conflict and lower evidence confidence rather than averaging it away.
- Scores are decision support, not automatic CRM actions.
