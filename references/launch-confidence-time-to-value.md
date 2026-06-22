# Launch DQ / Launch Time-to-Value — Peer Handoff

**Project site:** https://launch-dq.quick.shopify.io/#v3-overview  
**Prepared for:** Qualification / Opportunity Confidence project integration  
**Prepared by:** Matt Ward context package  
**Prepared on:** 2026-06-10  
**Use:** Internal Shopify handoff for a teammate adding launch-readiness, launch-confidence, and time-to-value features into a qualification workflow.

> **Internal handling note:** This file references internal Shopify data sources, launch-case outcomes, merchant examples, and model logic. It is intended for internal qualification-tooling work, not external merchant sharing.

---

## Table of contents

1. [What this project is](#1-what-this-project-is)
2. [What I found locally](#2-what-i-found-locally)
3. [Executive summary](#3-executive-summary)
4. [Feature set to add to the qualification project](#4-feature-set-to-add-to-the-qualification-project)
5. [Data / input model](#5-data--input-model)
6. [Launch definitions to keep separate](#6-launch-definitions-to-keep-separate)
7. [Model mechanics](#7-model-mechanics)
8. [Risk signals, blockers, and credits](#8-risk-signals-blockers-and-credits)
9. [Recommended output contract](#9-recommended-output-contract)
10. [Discovery questions and missing-input behavior](#10-discovery-questions-and-missing-input-behavior)
11. [Calibration evidence](#11-calibration-evidence)
12. [Quantitative findings / priors from V1](#12-quantitative-findings--priors-from-v1)
13. [Qualitative failure archetypes](#13-qualitative-failure-archetypes)
14. [Data gathering and query patterns](#14-data-gathering-and-query-patterns)
15. [UI / workflow guidance](#15-ui--workflow-guidance)
16. [Implementation backlog](#16-implementation-backlog)
17. [Data caveats](#17-data-caveats)
18. [Source lineage](#18-source-lineage)
19. [Recommended next step](#19-recommended-next-step)

---

## 1. What this project is

Launch DQ started as a pre-sales disqualification analysis: which opportunities looked like revenue at close, but did not become healthy launched merchants?

V1 answered this statistically. V2 turned the findings into a Gantt / estimated-time-to-launch frame. V3 packages the logic as a portable **Launch Time-to-Value skill** that can run inside Pi / SE Assistant / Opportunity Confidence-style workflows.

The point of the project is **not** to create a blunt “DQ this deal” model. The point is to create a launch-readiness component that helps AE / SE pods know:

- whether the deal should proceed;
- whether missing launch context should be validated first;
- whether the merchant should hold / stay current plan / disqualify;
- how long it will likely take to reach first measurable value;
- what actions would improve the launch path.

Core distinction:

```text
Product Fit ≠ Launch Capacity ≠ Time to Value
```

The model predicts **time to first measurable value**, not merely detected launch, plan conversion, case stage, or store existence.

---

## 2. What I found locally

The full local context is available under Matt’s machine. These are the key artifacts to reference or port.

### 2.1 Primary Launch DQ project files

| Path | Purpose |
|---|---|
| `Desktop/Claude/launch-dq-site/index.html` | Local source for the deployed Quick site. Includes V1, V2, V3 sections. |
| `Desktop/Claude/launch-dq-site/launch-dq-v1-analysis-and-findings.md` | V1 statistical analysis and findings. |
| `Desktop/Claude/launch-dq-site/launch-dq-v2-gantt-ttv-context.md` | V2 Gantt / ETTL / TTV context. |
| `Desktop/Claude/launch-dq-site/launch-dq-v3-skill-context.md` | V3 skill context and summary of skill behavior. |
| `Desktop/Claude/launch-dq-site/launch-dq-stat-highlights-deck.html` | Highlights deck for stats. |

### 2.2 Launch Time-to-Value skill package

The skill package exists in two identical locations:

```text
Desktop/Claude/skills/launch-time-to-value/
/Users/mattward/.pi/agent/skills/launch-time-to-value/
```

Key files:

| File | Purpose |
|---|---|
| `SKILL.md` | Agent instructions, workflow, confidence rules, and output guidance. |
| `README.md` | Overview and trigger examples. |
| `references/model_v1.yaml` | Canonical seed model constants: baselines, penalties, credits, blockers, D2C priors, output guidance. |
| `references/algorithm.md` | Step-by-step model algorithm. |
| `references/input-schema.json` | Normalized input schema. |
| `references/output-schema.json` | Normalized output schema. |
| `references/questions.md` | Missing-context questions. |
| `references/queries.md` | Salesforce / BigQuery / local search query patterns. |
| `references/source-context.md` | Concise source summary loaded by the skill. |
| `references/calibration_cases.md` | B2B / mixed calibration suite. |
| `references/calibration_outputs.json` | Structured B2B / mixed calibration outputs. |
| `references/calibration_cases_d2c.md` | D2C / Plus calibration suite. |
| `references/calibration_outputs_d2c.json` | Structured D2C calibration outputs. |
| `references/marshall_river_slack_findings_2026-06-01.md` | Plus TTV driver research from Marshall / River Slack thread. |
| `references/session_transcript_2026-06-02.md` | DQ AI Skill demo feedback and product / governance decisions. |

### 2.3 Supporting analysis files

| Path | Purpose |
|---|---|
| `Desktop/Claude/launch-cases-brief.md` | Full-scale statistical brief. |
| `Desktop/Claude/dq-knowledge-base.md` | Pre-sales DQ knowledge base. |
| `Desktop/Accounts/closed-launch-analysis-2026-05-19.md` | Qualitative closed-launch analysis, archetypes, and initial ETTL model. |
| `Desktop/Claude/dq-jeopardy-shortlist-v3.md` | Launch-case ground truth for DQ Jeopardy. |
| `Desktop/Claude/dq-jeopardy-validation-report.md` | DQ Jeopardy validation report. |
| `Desktop/Claude/dq-jeopardy-handoff-FINAL.md` | Full handoff for DQ Jeopardy content. Useful pattern library. |
| `/Users/mattward/.pi/agent/skills/launch-inspector/SKILL.md` | Launch Inspector skill used for active launch case monitoring. |
| `launch-inspector-data.json` | Current Launch Inspector data. Last sync: 2026-06-09. |
| `.claude/cache/launch-inspector-summary-20260609T163231Z.md` | Current Launch Inspector summary. |

### 2.4 Source references captured in local notes

```text
Quick site: https://launch-dq.quick.shopify.io/#v3-overview
Slack thread: https://shopify.slack.com/archives/C0B0ZKLP9MW/p1780351136528639
Google Doc transcript: https://docs.google.com/document/d/1XTCTDvmTpz47CdBsAZRuTa7qPrYqTBOElibuH6WBX0A/edit?tab=t.t0i21sq57r4z
```

The Slack and Google Doc findings are already distilled into local references; you should not need to re-pull them to implement V1.

---

## 3. Executive summary

The qualification project should add a reusable **Launch Confidence / Time-to-Value component**.

This component should:

1. Read VTP / mutual accountability plan, Salesforce opportunity, PMF, launch telemetry, and historical launch patterns.
2. Check hard blockers first.
3. If blocked, return blocker / revalidation mode rather than a fake date.
4. If not blocked, calculate ETTL and TTV bands.
5. Attach confidence to the TTV band.
6. Show missing critical inputs and next-best actions above the fold.
7. Store outputs for reassessment and actual-outcome calibration.

Key product principles:

- **Predict first value, not plan conversion.**
- **Native paid activity beats admin activity.** Draft orders, imports, test activity, sandbox activity, and partner-only progress are not first value.
- **Product fit and launch capacity are separate axes.**
- **Missing context should trigger Validate first, not overconfident DQ.**
- **Strong commercial intent + solvable technical path should avoid false disqualification.**
- **Target dates can be floors.** If the merchant has a credible sequenced plan, do not predict faster than their own path without evidence.
- **Current expected launch date is re-baselined.** Preserve original committed / proposed launch date and estimate-change count.
- **The long-term home is Opportunity Confidence.** The skill can run standalone, but should likely appear as a nested launch confidence / TTV section in an OC report.

---

## 4. Feature set to add to the qualification project

### 4.1 Above-the-fold Launch Confidence card

Add a visible card near the top of the qualification report.

| Field | Description |
|---|---|
| `recommendation` | Proceed / Proceed with plan / Validate first / Hold / Stay current plan / Disqualify. |
| `launch_confidence` | High-level read on whether the merchant is likely to complete launch. |
| `ttv_band` | P25 / P50 / P75 / P90 TTV days and calendar dates where possible. |
| `evidence_confidence` | Confidence in the TTV score itself. |
| `primary_blocker_or_risk` | Main blocker or risk affecting launch path. |
| `missing_critical_inputs` | Highest-impact missing fields, with owner and exact question. |
| `next_best_action` | Single most important immediate action. |
| `first_value_definition` | What counts as first measurable merchant value. |

### 4.2 Recommendation ladder

| Recommendation | Meaning |
|---|---|
| `Proceed` | Strong commercial intent, strong fit, ready owner model, launch path already clear. |
| `Proceed with plan` | Good deal, but needs specific pre-close / launch actions. |
| `Validate first` | Potentially good deal, but a critical owner, SOW, product edge case, value metric, or launch assumption needs proof. |
| `Hold` | Real interest but weak near-term event, weak ownership, no launch intent, stale momentum, or future-dated readiness. |
| `Stay current plan` | Merchant may fit Shopify but lacks strong Plus / B2B / POS value case today. |
| `Disqualify` | No feasible path to first value, launch-critical PMF 4 blocker, no owner after validation, or unsolvable commercial / technical issue. |

### 4.3 Output modes

Add a `mode` so the UI can show grey blocked rows instead of forcing red numeric forecasts.

| Mode | When to use |
|---|---|
| `numeric_ttv` | Inputs are sufficient and no blockers fire. |
| `blocker` | Hard blocker exists; no numeric TTV until resolved. |
| `revalidate_stale_case` | Active or old launch case needs updated merchant / partner confirmation. |
| `timeline_reset` | Scope pivot invalidates the original launch plan. |
| `conditional_ttv` | Optional post-blocker range clearly labeled as conditional. |

### 4.4 Dynamic reassessment

Each assessment should expire or trigger re-run.

Default:

- `assessment_ttl_days`: 14
- Re-run on VTP update, PMF update, target-date movement, merchant silence, partner SOW signed/delayed, scope change, launch case status change, first-value evidence, churn/downgrade outcome.

### 4.5 Feedback / actual outcome capture

Capture:

- Was the recommendation useful?
- Was the TTV band plausible?
- Which missing inputs actually mattered?
- Actual close date.
- Actual launch date.
- Actual first-value date.
- Churn / downgrade outcome.
- Which driver was wrong or missing?

---

## 5. Data / input model

The model should normalize data from four source groups:

1. VTP / MAP / launch plan
2. Salesforce opportunity
3. PMF / TA
4. Launch telemetry / Launch Inspector

### 5.1 Merchant fields

| Field | Why needed |
|---|---|
| Merchant name / account id / shop ids | Identity and telemetry lookup. |
| Industry / vertical | Risk prior and stakeholder checklist. |
| Country / region | Geographic context and known data quirks. |
| Segment | Enterprise / LA / MM / SMB affects baseline. |
| Launch service model | Stronger TTV driver than industry. |
| Prior platform / migration source | NetSuite, SFCC, Adobe/Magento, custom, BigCommerce, VTEX, Lightspeed, etc. |
| SKU count | 10k+ / 100k+ creates catalog complexity. |
| POS location count | 6+ / 26+ affects launch duration. |
| Products in scope | Plus, B2B, POS Pro, Professional Services, Markets / International, etc. |

### 5.2 Salesforce opportunity fields

| Field group | Fields |
|---|---|
| Identity | Opportunity id, name, account id. |
| Stage | Stage, stage age, created date, close date, last activity date. |
| Commercial | Amount, ARR, verified revenue, source, opportunity type. |
| Product | Primary product line, product lines in scope. |
| Team | SE attached, CSM attached, partner, Launch, Pro Services. |
| Compelling event | Present, type, date, evidence. |
| Stakeholders | Count, function count, decision maker, champion, operator, IT/integration, roles present, role evidence source. |
| Pre-sale breadth | Contact count, role count, Tech/IT, Ops/Wholesale, ecommerce/digital, founder-only. |
| Language flags | Hold, winback, recovered prior CL, generic manual-process story, NetSuite expiry only, new champion launch ASAP, consultant-driven, short feature-list notes. |
| Momentum | Last inbound/outbound, typical response time, current response gap, response gap multiplier. |

### 5.3 VTP / MAP / launch plan fields

| Field group | Fields |
|---|---|
| Dates | Target launch date, target first-value date, original committed launch date, current expected launch date, estimate change count, slip vs original. |
| First value | Metric defined, metric, threshold, horizon, evidence. |
| Merchant PMO | Internal PM, sponsor, operational owner, decision cadence, day-2 incident owner. |
| Integration | ERP/middleware in scope, ERP name, owner, SOW, connector pattern, custom/legacy, EDI/punchout day-one. |
| Partner | Partner name, involved, SOW, scope coverage, PMO coverage, delay signal, reference same-pattern build. |
| Scope | MVP vs phase 2, multi-market/brand, unbounded scope, data migration. |
| Channel migration | Existing channel, sunset plan, rep-keyed order motion, first buyer cohort. |
| Responsiveness | Days since merchant touch, partner health, merchant responsiveness. |

### 5.4 PMF fields

| Field | Why needed |
|---|---|
| PMF present / assessment date / stale flag | Source confidence. |
| Overall PMF | High-level product fit. |
| Category PMF scores | Category-level complexity maps to penalties / blockers. |
| PMF 0 unanswered MVDQs | Missing discovery. |
| PMF 2 / 3 launch-scope categories | Complexity penalties. |
| PMF 4 launch-critical | Block unless scoped around. |
| Future-phase PMF 4 | Future risk only, unless dependency for first value. |
| Roadmap dependencies | Need capability, owner, date, whether before target launch. |
| Partner workarounds | Need same-pattern reference, owner, cost/timeline. |

### 5.5 Launch telemetry fields

| Field | Why needed |
|---|---|
| Launch case id / status / exit reason / closed reason | Actual launch state / blocker mode. |
| Original proposed date / expected date / estimate changes | Re-baselining and slip risk. |
| Days since merchant / partner touch | Responsiveness risk. |
| Build/test delay / partner delay / merchant prioritization / unresponsive signals | Major overrun signals. |
| B2B company count / B2B orders / native paid B2B orders / B2B GMV % | B2B first-value proof. |
| Imported or draft order spike | Vanity-launch guardrail. |
| DTC orders / DTC GMV % | DTC first-value proof. |
| Password / domain status | Technical go-live signal for acquisition, not value by itself. |

---

## 6. Launch definitions to keep separate

| Definition | Meaning | Do not conflate with |
|---|---|---|
| Technical go-live | Store exists, domain/password changed, checkout works, case moved stage. | First measurable value. |
| Detected launch | Warehouse / launch-case signal such as GMV threshold or launch status. | Durable value. |
| First measurable value | Production value signal tied to product line. | Draft/import/test/sandbox/partner activity. |
| Durable value | d30/d90/d365 adoption, GMV, Plus value-driver use, downgrade risk. | First transaction only. |

Preferred first-value definitions:

| Product line | First-value metric |
|---|---|
| B2B | First named buyer cohort places paid/native B2B orders; 0.5% verified annual B2B GMV; non-import order velocity. |
| Plus / DTC | Non-test paid DTC order velocity; d30 GMV; Plus value-driver adoption. |
| POS Pro | Store rollout coverage, hardware live, staff trained, first production POS transactions. |
| Retail / omnichannel | BOPIS / inventory / fulfillment workflow live in production. |
| Mixed | Slowest critical product path, with separate first-value metrics per workstream. |

---

## 7. Model mechanics

### 7.1 Formula

```text
ETTL_days = baseline_remaining_days(stage or launch_case_service_model)
          + product / segment / industry priors
          + risk_signal_penalties
          + PMF_complexity_penalties
          + VTP_readiness_penalties
          − qualification_credits

TTV_days  = ETTL_days
          + value_realization_days(product_line, implementation_readiness)
          + blocker_resolution_days(open_blockers)
          + target_date_floor_if_credible
```

### 7.2 Stage baselines

Use when pre-close or no empirical launch-case baseline exists.

| Stage | Baseline days |
|---|---:|
| Pre-Qualified / Qualified | 180 |
| Discovery | 160 |
| Demonstrate | 150 |
| Envision | 120 |
| Solution | 100 |
| Deal Craft | 75 |
| Negotiation | 60 |
| Closed Won — Build | 60 |
| Closed Won — Test | 14 |
| Closed Won — Launch | 7 |
| Unknown | 150 |

### 7.3 Empirical D2C / Plus launch-case baselines

Use these for existing Mid-Market D2C / Plus Launch Cases.

| Product / service model | Cases | Launched with TTV | Avg | p25 | p50 | p75 | p90 | Churned | Open 180d+ |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Plus / Mid-Market Scaled | 1,538 | 1,040 | 129d | 57d | 115d | 182d | 251d | 48 | 89 |
| Plus / Mid-Market Assigned | 949 | 585 | 123d | 54d | 98d | 172d | 248d | 24 | 27 |

Rule:

```text
If existing MM D2C / Plus Launch Case:
  baseline = empirical service-model TTV baseline
  value_realization_days = 0 unless VTP defines later first value
```

### 7.4 Plus TTV driver benchmarks

Plus launched 2024+:

- Median TTV ≈ 115d.
- Mean TTV ≈ 139d.
- Partner-led: 151d median vs 92d without partner.
- Enterprise: ~220d.
- Large Accounts: ~177d.
- Mid-Market: ~127d.
- NetSuite: ~195d.
- SFCC: ~159d.
- PrestaShop/custom: ~152-153d.
- Adobe/Magento: ~142d.
- BigCommerce: ~116d.
- WooCommerce: ~132d.
- OpenCart: ~103d.
- VTEX: ~105d.
- Lightspeed: ~84d.
- 100k+ SKUs: ~187d vs ~130d for <100 SKUs.
- 26+ POS locations: ~185d vs ~127d with no POS.
- Professional Services in scope: ~258d.
- B2B in scope: ~134d vs ~116d baseline.
- Managed / International Markets only: ~106d; not slower by default.

### 7.5 Original launch date behavior

Always track:

- original proposed / committed launch date;
- current expected launch date;
- estimate change count;
- slip vs original.

Why:

- Current expected launch date is continuously re-baselined.
- Median slip vs original: +44d.
- Build/Test Delay: median +101d.
- Partner Delay: median +122d.
- Merchant Prioritization: median +81d.
- Unresponsive Merchant: median +57d.

### 7.6 Value-realization days

Add after ETTL unless the baseline is already TTV-like.

| Context | Days |
|---|---:|
| B2B native, no ERP | +30 |
| B2B known ERP connector + SOW | +45 |
| B2B custom / legacy ERP | +90 |
| B2B custom ERP merchant-owned | +45 |
| B2B EDI or punchout day one | Block |
| Plus existing shop / plan conversion | +30 |
| Plus MM launch case detected value | +0 |
| Plus new replatform | +60 |
| Plus headless / custom stack | +90 |
| DTC existing shop | +30 |
| DTC new replatform | +60 |
| DTC headless / Oxygen | +90 |
| POS Pro single region | +45 |
| POS Pro multi-location | +75 |
| Retail omnichannel complex | +90 |
| Mixed product lines | Use max critical path |
| Unknown | +60 |

### 7.7 Confidence and bands

Confidence applies to the TTV band.

| Confidence | Requirements | Band spread |
|---|---|---|
| HIGH | VTP, SF opp, PMF/TA, stakeholder roles, first-value metric, known owners, credible target date. | P25 = 0.80× P50, P75 = 1.25×, P90 = 1.60×. |
| MEDIUM | SF + either VTP or PMF. One major source missing/partial. | P25 = 0.70×, P75 = 1.40×, P90 = 1.90×. |
| LOW | Metadata only, no VTP/PMF, stale target, unknown owners, no first-value metric. | P25 = 0.60×, P75 = 1.65×, P90 = 2.30×. |
| BLOCKED | Launch-killer pattern fires. | No numeric TTV. |

---

## 8. Risk signals, blockers, and credits

The canonical source is `references/model_v1.yaml`. Below is the implementation-ready subset to port first.

### 8.1 Hard blockers

Return no numeric TTV until resolved.

| Blocker | Recommended behavior |
|---|---|
| Launch-critical PMF 4 platform limit | Disqualify or scope around blocker. |
| B2B requires EDI / punchout day one | Disqualify or scope around blocker. |
| No feasible payment path for day-one checkout | Validate first. |
| No implementation owner and no partner | Hold. |
| No first-value metric | Validate first. |
| No VTP / launch plan for complex B2B | Validate first. |
| No launch intent | Hold / close launch case. |
| D2C no merchant or partner response after burndown | Hold / close launch case. |

### 8.2 Launch-killer combinations

| Pattern | If | Output | Recommendation |
|---|---|---|---|
| Pattern C solo/stale/no-DM | Solo stakeholder + no DM role + stale >60d | `N/A — pre-launch blocked: Pattern C solo/stale/no-DM risk` | Hold |
| Pattern A dormant | Hold/winback + stale >60d | `N/A — pre-launch blocked: Pattern A dormant / re-engagement required` | Hold |
| Pattern B vanity launch | Test/Launch + imports/drafts counted as value | `N/A — value not proven: verify paid/native transactions` | Validate first |
| Pattern D channel migration | Existing B2B channel + no sunset plan + late-stage/CW | `N/A — commercial blocker: channel migration/sunset plan required` | Validate first |
| Complex B2B no ERP owner | B2B + ERP/middleware in scope + ERP owner unknown | `N/A — pre-launch blocked: ERP/middleware owner unknown` | Validate first |
| D2C no launch intent | No launch intent or merchant/partner unresponsive | `N/A — pre-launch blocked: no D2C launch intent / no response` | Hold |
| D2C scope pivot | Plus/D2C + B2B↔D2C pivot + out-of-scope requirements | `N/A — timeline reset required` | Validate first |

### 8.3 Important risk penalties

| Signal | Days / behavior |
|---|---:|
| No compelling event LA | +90 |
| No compelling event MM | +30 |
| No launch intent | Block |
| Stale activity >60d | +45 |
| Hold / winback language | +60 |
| Recovered prior closed-lost without new CE | +60 |
| Solo stakeholder | +30 |
| No second function engaged | +30 |
| No internal PM named | +45 |
| No operational owner named | +45 |
| Founder-only, no operator | +30 |
| No merchant-side launch owner | +45 |
| ERP owner unknown | +60 |
| Middleware owner unknown | +45 |
| No partner SOW for partner-led build | +45 |
| Partner scope frontend-only | +60 |
| Partner delay signal | +120 |
| Build/test delay signal | +100 |
| NetSuite / ERP-coupled migration source | +80 |
| SFCC migration source | +45 |
| Custom platform / PrestaShop | +40 |
| Adobe / Magento | +30 |
| D2C ERP implementation delay | +90 |
| Headless / Oxygen deployment blocker | +60 |
| External checkout / GMV attribution issue | +45 |
| Scope pivot B2B↔D2C | +120 |
| Requirements out of original partner scope | +90 |
| MVP vs phase 2 not agreed | +45 |
| First-value metric missing | +30 or blocker |
| Existing B2B channel no sunset plan | +45 |
| Imported / draft orders counted as value | +60 / blocker guardrail |
| 100k+ SKUs | +57 |
| 10k+ SKUs | +25 |
| 26+ POS locations | +58 |
| 6-25 POS locations | +25 |
| Professional Services in scope | +100 |
| B2B in scope on Plus launch | +18 |

### 8.4 PMF penalties

| PMF signal | Behavior |
|---|---|
| PMF 0 unanswered launch-critical MVDQ | +25d per category, cap +75d. |
| PMF 2 complexity | +20d per category, cap +80d. |
| PMF 3 high complexity | +45d per category, cap +135d. |
| PMF 4 launch-critical | Block. |
| PMF 4 future phase | No launch penalty unless dependency for first value. |
| Total PMF cap | +180d. |

### 8.5 Qualification credits

Apply only with explicit evidence. Cap total credits at -120d.

| Credit | Days |
|---|---:|
| Documented compelling event | -20 |
| Realistic target launch date | -10 |
| Economic buyer / DM role tagged | -10 |
| Champion role tagged | -15 |
| Recent activity <14d | -10 |
| 3+ stakeholders | -20 |
| 2+ functions engaged | -20 |
| Internal PM named | -30 |
| Operational owner named | -20 |
| VTP / MAP complete | -30 |
| MVP / phase 2 agreed | -25 |
| First-value metric defined | -20 |
| First 3 B2B buyers named | -15 |
| Partner SOW covers frontend/backend/data/integration | -30 |
| ERP owner and SOW confirmed | -40 |
| Known Shopify connector pattern | -20 |
| Existing Plus customer / no platform migration | -20 |
| Existing B2B channel sunset plan | -20 |
| CSM / success owner assigned | -15 |
| SE attached | -15 |
| Same-pattern partner reference | -15 |
| Merchant-owned integration team confirmed | -30 |
| Clear phased rollout plan | -20 |
| D2C compelling platform pain confirmed | -20 |
| D2C partner build active and on timeline | -20 |
| D2C storefront live confirmed | -30 |
| D2C target current and credible | -10 |
| Lightspeed / VTEX / OpenCart migration source | -15 |
| Simple DTC scope confirmed | -10 |
| Roadmap available before target launch | -15 |
| Documented partner workaround same pattern | -15 |

### 8.6 False-disqualification guardrail

Before returning `Disqualify`, check:

- Is commercial intent strong and current?
- Is there a credible buyer / sponsor?
- Is the technical issue solvable through roadmap, partner workaround, scope reduction, or phase 2?
- Are critical sources missing?

If yes, prefer `Validate first`, `Proceed with plan`, or `Hold` over `Disqualify`.

---

## 9. Recommended output contract

### 9.1 Compact object

```json
{
  "merchant": {"name": "Merchant", "account_id": null, "opportunity_id": null, "launch_case_id": null},
  "assessment_date": "2026-06-10",
  "model_version": "launch_ttv_model_v1",
  "recommendation": "Proceed with plan",
  "confidence": "HIGH",
  "mode": "numeric_ttv",
  "confidence_detail": {
    "ttv_score_confidence": "HIGH",
    "confidence_reasons": ["rich VTP", "multi-stakeholder map", "credible target date"],
    "missing_information_impact": "Exact P50 remains MEDIUM until integration owner and SOW timing close",
    "band_width_reason": "P75/P90 widened for open integration-owner risk"
  },
  "blocker": null,
  "first_value_definition": {
    "defined": true,
    "metric": "first paid/native B2B orders from named buyer cohort",
    "threshold": "3 named buyers or 0.5% verified annual B2B GMV",
    "horizon": "d30",
    "evidence": "VTP"
  },
  "ettl": {
    "p50_days": 105,
    "calendar_date": "2026-09-01"
  },
  "ttv": {
    "p25_days": 120,
    "p50_days": 145,
    "p75_days": 180,
    "p90_days": 230,
    "calendar_p50_date": "2026-10-01",
    "band": "amber",
    "value_realization_days": 30,
    "target_anchor": {
      "applied": true,
      "target_launch_date": "2026-09-01",
      "raw_model_ttv_days": 130,
      "anchor_reason": "VTP target is credible and later than raw model",
      "target_infeasible": false
    }
  },
  "guardrails": {
    "false_disqualification_check": {
      "passed": true,
      "reason": "Strong commercial intent plus solvable technical path = validate before DQ",
      "missing_context_prevents_dq": true
    },
    "launch_definition_check": {
      "technical_go_live_distinct_from_value": true,
      "detected_launch_threshold_used": "not sufficient alone",
      "threshold_fit_for_product_line": false,
      "caveat": "Use paid/native activity, not universal GMV threshold only"
    }
  },
  "drivers": {
    "penalties": [],
    "credits": [],
    "top_driver_summary": []
  },
  "missing_inputs": [],
  "gantt_milestones": [],
  "next_best_actions": [],
  "source_audit": [],
  "reassessment": {
    "assessment_ttl_days": 14,
    "recommended_rerun_date": "2026-06-24",
    "rerun_triggers": ["VTP updated", "partner SOW signed", "target date moved", "merchant response gap >3x typical"]
  },
  "feedback_capture": {
    "enabled": true,
    "actual_outcome_fields": ["closed_won_date", "launch_date", "first_value_date", "churn_date"]
  },
  "human_summary_markdown": "..."
}
```

### 9.2 Missing input shape

Every missing input should include owner, impact, and exact question.

```json
{
  "field": "BC integration owner",
  "owner": "Merchant",
  "why_needed": "Longest-lead launch dependency",
  "impact_if_missing": "Caps TTV confidence at MEDIUM and may move recommendation to Validate first",
  "question_to_ask": "Who owns Business Central customer-ID mapping and by what date is the SOW signed?"
}
```

### 9.3 Standard Gantt milestones

| Milestone | Typical owner | Due |
|---|---|---|
| Qualification complete | AE + SE | Pre-proposal |
| Product fit validated | SE | Pre-proposal |
| Launch readiness validated | SE + partner + merchant PM | Pre-close |
| ERP / integration owner confirmed | Merchant PM / Partner | Pre-close |
| Partner SOW accepted | Partner + merchant | Pre-close |
| MVP vs phase 2 signed off | AE + SE + merchant | Pre-close |
| First-value metric defined | AE + SE + Launch / CSM | Pre-close / handoff |
| Launch handoff accepted | Launch | Close / kickoff |
| First buyer cohort / pilot live | Merchant + Launch | Launch / d30 |
| d30 / d90 value check | CSM + Launch | Post-launch |

---

## 10. Discovery questions and missing-input behavior

Ask only questions that resolve blockers or materially improve confidence.

### 10.1 Minimum questions when no VTP exists

1. What is the target first-value metric?
2. Who owns implementation on the merchant side?
3. Is ERP / middleware in launch scope? If yes, who owns it and is there an SOW?
4. Which partner(s) are required, and what scope do they own?
5. What is in launch MVP vs phase 2?
6. What existing B2B / POS / DTC process is being replaced, and when does it sunset?

### 10.2 Commercial intent / momentum

- What dated external event forces this launch?
- Is there a contract expiry, peak season, compliance deadline, current-platform sunset, or board mandate?
- Who is the economic buyer?
- What happens if the merchant does nothing for 6 months?
- Is budget approved for Shopify and partner work?
- What is typical response time, and what is the current gap?
- Has response time degraded by 3× or 5×?

### 10.3 Stakeholder / capacity

- Who is the second stakeholder from a different function?
- Is there a named merchant-side launch owner separate from executive sponsor / founder?
- For B2B / ERP-heavy deals, who is Tech/IT or integration lead?
- For B2B / wholesale, who is Ops / wholesale owner?
- For founder-led deals, who is the operator / ecommerce / IT owner beyond founder?
- Who has led a software implementation of this scope before?
- What work gets deprioritized so this implementation has calendar space?
- Who owns day-2 operational incidents?

### 10.4 B2B-specific

- Who owns B2B P&L separately from DTC / Plus?
- Are the first three buyer companies named?
- Will buyers order directly, or will reps key orders?
- Will draft/import orders count as launch activity? If yes, what separate metric proves buyer adoption?
- What legacy B2B channel is being replaced and when will it sunset?
- Are catalogs, companies, locations, net terms, payment methods, and buyer permissions in MVP?

### 10.5 Complexity / migration

- What platform / system is the merchant migrating from?
- Is the source platform ERP-coupled or bespoke?
- How many SKUs are in MVP? Above 10k or 100k?
- Any fitment, configurator, bundle, compliance, localization, product-media, or variant-matrix requirements?
- How many POS locations are in MVP? Above 6 or 26?
- Which products are in scope?

### 10.6 Launch definition

- What event counts as technical go-live?
- What event counts as detected launch?
- What event counts as first measurable value?
- Is the threshold GMV %, named buyer cohort, first paid/native order, POS transaction, Plus value-driver adoption, or something else?
- How will we distinguish imports, drafts, sandbox activity, marketplace syncs, or partner tests from merchant value?

---

## 11. Calibration evidence

### 11.1 B2B / mixed calibration cases

| Case | Archetype | Known outcome | Actual signal | Expected model behavior |
|---|---|---|---:|---|
| Cressi EU | Successful recovered launch | Launched after delayed handoff | ~85d CW → launch | Predict ~90-120d TTV; stakeholder credits validated. |
| HeatWaveVisual | Strong-fit ghosted | Churn recommended after silence | ~106d TA → churn rec; ~118d won→churn | Block / Hold due solo stakeholder + no PMO. |
| Rainier / Katool | Vanity-launch risk with native value | Test→Launch spike; filtered/native GMV threshold later | ~48d PMF → native GMV threshold | Proceed with plan but validate paid/native proof. |
| Takedown Sportswear | Technical launch, commercial loss | Churn recommended despite launch work | ~112d CW → churn rec | Add day-2 finance/ops risk; 120-160d range. |
| Swig Life | Complex B2B future target | Build, no activity, target July/Aug | ~180-210d target | Target-date anchor; ~190-230d TTV. |
| New Chapter | High-context PMF 2 planned launch | Rich plan, Sept 1 target | ~92d to launch; first value likely d30 | Proceed with plan; ~120-150d TTV; validate BC/SOW. |

V3 site highlights:

1. HeatWaveVisual — product fit ≠ launch capacity.
2. Rainier / Katool — paid/native validation required.
3. New Chapter — complex but high-context proceed-with-plan.

### 11.2 D2C / Plus calibration cases

| Case | Actual outcome | Actual TTV / age | Key signal | Expected model behavior |
|---|---|---:|---|---|
| Luxo Living | Launched | 60d | Platform pain / dev reliance | Numeric TTV with D2C platform-pain credit. |
| Wallace Cotton | Launched | 110d | MM Scaled median case | Numeric around p50 115d. |
| Vahro | Launched | 180d | External checkout / GMV attribution | Numeric with attribution warning. |
| Paper Roll Supplies | Launched after extreme delay | 628d | B2B→D2C pivot, out-of-scope requirements | Timeline reset, not numeric forecast. |
| KINOKUNIYA | Launch Departure - Churn | N/A | No launch intent | Block no-launch-intent. |
| Contents Works | Launch Departure - Churn | N/A | No launch intent, no partner, Oxygen issues | Block no-launch-intent / no response. |
| CampuStore | Stale open | >600d open | Partner independent, merchant not cc’d | Revalidate stale case. |
| Bed Bath & Beyond | Stale open | >700d open | ERP implementation delay | Revalidate stale D2C ERP case. |

D2C validation sanity check:

- Clean numeric launched cases: Luxo, Wallace Cotton, Vahro.
- Mean absolute error: 21d.
- Median absolute error: 18d.
- 3 of 3 actuals inside empirical p25-p90 band.

### 11.3 Launch-case ground truth from DQ Jeopardy

For last-12-month MMLA CW opps joined to launch cases:

| Outcome | n | Approx share | Meaning |
|---|---:|---:|---|
| Launched | 685 | ~46% | Win that stuck. |
| Launch Departure — Churn | 117 | ~8% | Forced win. |
| Launch Departure — Downgrade | 2 | <1% | Won at one tier, downgraded. |
| Stale open Build/Test/Launch ≥240d | ~91 | ~6% | High-risk stuck launches. |
| No launch case | 6,098 | ~41% | Often legitimate cross-sell / contract changes, but can hide ghost wins. |

Interpretation: about **14% of MMLA closed-won deals either churned outright or were stuck in stale launch cases** in that scan.

---

## 12. Quantitative findings / priors from V1

### 12.1 Headline signals

| Metric | Finding | Use |
|---|---:|---|
| Baseline never-launch rate | 47.7% among 30,786 industry-known won opps | Risk narrative, not merchant-level probability. |
| SE attachment | No-SE 55.9% never-launch vs SE-attached 29.1%; 26.8pp lift | `se_attached` readiness credit. |
| Closed-lost timing reasons | 53% | Timing / readiness / unresponsive language matters. |
| LPV churn with CSM | 0.48% no CSM vs 0.12% with CSM | CSM / success owner affects Plus value. |
| Merchant-driven churn split | 78% downgrade / 22% cancellation | Need Plus-value qualification, not just Shopify fit. |
| LA compelling event | 0% never-launch with CE vs 29.8% without CE | Missing CE severe for LA; noisy for MM. |

### 12.2 GMV ramp

| Percentile | d30 GMV | d365 GMV |
|---|---:|---:|
| p25 | $28K | $328K |
| p50 | $117K | $1.37M |
| p75 | $370K | $4.42M |
| p90 | $997K | $12.28M |

Use d30 GMV as a CSM watchlist trigger. A merchant can technically launch but fail to realize durable value.

### 12.3 Product / segment never-launch priors

| Segment × Product | Never-launch rate |
|---|---:|
| LA × POS Pro | 34.9% |
| MM × B2B | 25.2% |
| LA × Plus | 24.0% |
| MM × Plus | 17.0% |
| MM × POS | 15.0% |
| LA × B2B | Observed 0.0%, but use non-zero floor due small/suspicious sample. |

### 12.4 Industry priors

High-risk industries:

- Entertainment: 62.3%
- Toys & Games: 61.8%
- Home Decor: 58.0%
- Vehicles: 54.7%
- Apparel: 53.5%

Lower-risk industries:

- Pharmaceuticals: 25.2%
- Aviation: 30.8%
- Tobacco: 31.3%
- Industrial Equipment: 34.1%
- B2B Wholesale: 36.5%
- Auto Parts: 37.0%
- Office Supplies: 39.2%
- Hardware & Tools: 39.6%
- Building Materials: 40.7%

Use industry as a prior, never as a hard DQ rule.

---

## 13. Qualitative failure archetypes

### Pattern A — Plus-expansion B2B never launched

Existing Plus DTC merchant; B2B sold as add-on; no implementation owner engages post-CW.

Signals:

- Sole stakeholder is commercial buyer, not operator.
- Existing B2B process works “well enough.”
- No partner and no internal implementation lead.
- No separate B2B P&L owner.

Question:

> Who owns the B2B P&L and implementation on the merchant side?

### Pattern B — Vanity launch

Case status moves Test → Launch due to imports/draft/test activity, not real buyer value.

Signals:

- Rep-keyed orders, CSV imports, draft order volume.
- No first three buyer companies.
- No buyer onboarding plan.

Question:

> What is the first real B2B transaction that proves launch worked, and how will we distinguish it from imports/drafts?

### Pattern C — STRONG FIT ghosted

High product fit but one overloaded stakeholder and no implementation capacity.

Signals:

- One person across discovery/demo/TA.
- Founder / GM / senior generalist is the only contact.
- Manual process implies upside but no software implementation muscle.
- No internal PM or kickoff date.

Question:

> Who else has confirmed the need, and who has led a software implementation of this scope before?

### Pattern D — Technical launch, commercial loss

Technical launch happens, but legacy process continues and Shopify does not absorb workload.

Signals:

- No sunset plan for existing channel.
- Multiple Shopify products at launch with no day-2 owner.
- Finance / ops issue owner unknown.

Question:

> What existing process is this replacing, when does it sunset, and who owns day-2 operational incidents?

### Six signal framework

| Signal | Detects | Patterns |
|---|---|---|
| S1 Solo stakeholder | Single-threading | A, C |
| S2 No DM role tagged | Weak authority evidence | C |
| S3 Stale activity >60d | Dormancy | A, C |
| S4 Opp name / language flags | Hold, winback, vague/additive | A, B, D |
| S5 Stuck stage >90d | Pipeline stagnation | A |
| S6 No activity history | Discovery gap | C |

---

## 14. Data gathering and query patterns

### 14.1 Salesforce opportunity

```sql
SELECT
  Id,
  Name,
  AccountId,
  Account.Name,
  Account.Industry,
  Account.BillingCountry,
  StageName,
  Amount,
  ARR__c,
  CloseDate,
  CreatedDate,
  LastActivityDate,
  LastModifiedDate,
  Type,
  LeadSource,
  Source_Channel__c,
  NextStep,
  Description,
  Compelling_Event__c,
  Compelling_Event_Date__c,
  Owner.Name,
  Owner.Email
FROM Opportunity
WHERE Id = 'OPPORTUNITY_ID'
   OR Account.Name LIKE '%MERCHANT%'
ORDER BY LastModifiedDate DESC
LIMIT 10
```

### 14.2 Opportunity contact roles

```sql
SELECT
  Id,
  OpportunityId,
  ContactId,
  Contact.Name,
  Contact.Title,
  Contact.Email,
  Role,
  IsPrimary
FROM OpportunityContactRole
WHERE OpportunityId = 'OPPORTUNITY_ID'
ORDER BY IsPrimary DESC, Role ASC
```

### 14.3 Opportunity team

```sql
SELECT
  Id,
  OpportunityId,
  User.Name,
  User.Email,
  TeamMemberRole
FROM OpportunityTeamMember
WHERE OpportunityId = 'OPPORTUNITY_ID'
```

### 14.4 Launch cases

```sql
SELECT
  Id,
  CaseNumber,
  Subject,
  Status,
  CreatedDate,
  LastModifiedDate,
  ClosedDate,
  Description,
  AccountId,
  Account.Name,
  ContactEmail,
  Contact.Name,
  Owner.Name
FROM Case
WHERE AccountId = 'ACCOUNT_ID'
  AND RecordType.Name = 'Launch'
ORDER BY CreatedDate DESC
```

### 14.5 Shop profile

```sql
SELECT
  shop_id,
  name,
  domain,
  permanent_domain,
  is_password_enabled
FROM `shopify-dw.accounts_and_administration.shop_profile_current`
WHERE shop_id = SHOP_ID
```

### 14.6 Native B2B activity

```sql
SELECT
  shop_id,
  COUNT(*) AS b2b_order_count,
  COUNT(DISTINCT company_id) AS company_count
FROM `shopify-dw.merchant_sales.b2b_orders_v2`
WHERE shop_id = SHOP_ID
  AND is_b2b_on_shopify = true
  AND NOT is_migrated_from_dtc
  AND NOT is_imported_by_3rd_party_app
GROUP BY shop_id
```

### 14.7 B2B GMV

```sql
SELECT
  SUM(total_price_usd) AS b2b_gmv
FROM `shopify-dw.orders.orders_current`
WHERE shop_id = SHOP_ID
  AND company_id IS NOT NULL
  AND test = false
  AND cancelled_at IS NULL
  AND created_at >= 'LAUNCH_START_DATE'
```

### 14.8 DTC orders / GMV

```sql
SELECT
  COUNT(*) AS dtc_order_count,
  SUM(total_price_usd) AS dtc_gmv
FROM `shopify-dw.orders.orders_current`
WHERE shop_id = SHOP_ID
  AND test = false
  AND cancelled_at IS NULL
  AND company_id IS NULL
```

### 14.9 Text signals to extract

Search notes / calls / Slack / email for:

- not ready, deprioritized, unresponsive, hold, winback;
- re-engaged, recovered, previously closed lost;
- manual process, bring online, spreadsheet, Excel, QuickBooks;
- NetSuite license, contract expiry, renewal;
- launch ASAP, as quick as possible, new champion;
- ERP, middleware, SAP, Dynamics, D365, NetSuite, WMS, OMS, EDI, punchout;
- SOW, scope, partner, frontend, backend, integration;
- MVP, phase 2, future, nice to have;
- first order, first buyers, pilot, buyer onboarding, sunset, legacy portal.

---

## 15. UI / workflow guidance

### 15.1 Single-opportunity summary

Example above-the-fold render:

```text
[Launch TTV] Merchant
Recommendation: Proceed with plan
Estimated TTV: P50 145d | P25-P75 120-180d | P90 230d
Confidence: HIGH launch path / MEDIUM exact P50
Primary risk: BC integration owner and SOW timing
First-value definition: first paid/native B2B orders from named buyer cohort
False-DQ guardrail: passed — strong commercial intent plus solvable technical path
Next best action: Confirm integration owner and SOW before proposal
```

Below the fold:

- Top drivers
- Missing inputs
- Gantt milestones
- Next-best actions
- Evidence / source audit
- Reassessment triggers

### 15.2 Portfolio triage mode

Support future weekly portfolio scan:

| Column | Description |
|---|---|
| Merchant | Account / opportunity / launch case. |
| Recommendation | Proceed / Validate / Hold / etc. |
| TTV band | P50 and range, or blocked. |
| Confidence | TTV confidence and evidence confidence. |
| Primary blocker | If any. |
| Next action | Owner-specific action. |
| TTL / rerun date | Assessment freshness. |
| Drift | TTV grew >30d, response gap, estimate moved, new blocker. |

### 15.3 Color bands

| Band | Rule |
|---|---|
| Green | P50 ≤120d, confidence high/medium, no blockers. |
| Amber | P50 121-180d or actions required. |
| Red | P50 ≥181d and numeric high-risk estimate. |
| Grey | Blocked, stale, unknown, or no numeric TTV. |

---

## 16. Implementation backlog

### P0 — Stable object and UI contract

1. Add normalized input object.
2. Add output object.
3. Add recommendation ladder and modes.
4. Add `missing_inputs`, `source_audit`, `confidence_detail`, `guardrails`, `reassessment`, `feedback_capture`.
5. Build above-the-fold Launch Confidence card.

Acceptance criteria:

- Missing first-value metric does not produce high-confidence TTV.
- Blockers return grey / N/A output with next action.
- Disqualify always includes false-DQ guardrail.

### P1 — Model runner

1. Implement baselines.
2. Implement hard blockers and launch-killer overrides first.
3. Implement penalties, PMF penalties, credits, caps.
4. Implement target-date anchor.
5. Implement confidence spreads.
6. Implement missing-input generation.

Acceptance criteria:

- HeatWaveVisual blocks / holds.
- Rainier / Katool warns on vanity launch.
- New Chapter returns Proceed with plan + integration/SOW validation.
- Luxo / Wallace / Vahro land inside empirical D2C bands.
- Paper Roll Supplies returns timeline reset.
- KINOKUNIYA / Contents Works block no-launch-intent.

### P2 — Data integrations

1. Salesforce opportunity, contact roles, team, activity, launch cases.
2. PMF / TA lookup.
3. VTP / MAP / launch plan lookup.
4. Launch telemetry / BigQuery / Launch Inspector.
5. Original launch date and estimate history.
6. Text-signal extraction.

Acceptance criteria:

- Role absence is marked “no evidence,” not confirmed absence.
- Current expected launch date is not treated as original baseline.
- D2C/Plus Launch Cases use empirical service-model baselines.
- Imports/drafts cannot count as first value.

### P3 — Calibration and governance

1. Add 10-20 more labeled outcomes.
2. Compare predicted P50/P75/P90 vs actual first-value dates.
3. Tune into `model_v2.yaml` or equivalent.
4. Store assessment history in context library / Opportunity Confidence history.
5. Add feedback capture.
6. Submit through Revenue Skills Graph / contributor skill before org-wide rollout.

---

## 17. Data caveats

1. `became_full_price_shop_at` is plan conversion, not true merchant go-live.
2. Detected launch often does not equal first value.
3. Churn reason data is Banff-heavy and not fully populated for Trident.
4. LA × B2B observed 0% never-launch is suspicious and should use a floor.
5. Compelling event is noisy in MM; do not hard-gate MM solely on CE field.
6. Industry and geography are priors, not hard DQ rules.
7. Partner involvement can mean harder build; only credit partner when scope/SOW are proven.
8. Current expected launch date is re-baselined; preserve original date.
9. Missing CRM role data means “no evidence,” not proof of absence.
10. D2C validation panel is small; use empirical baselines and keep tuning.
11. No launch case can be legitimate for cross-sell / contract changes.
12. Universal GMV threshold is not appropriate across B2B / DTC / POS / mixed launches.

---

## 18. Source lineage

### Local files

```text
Desktop/Claude/launch-dq-site/index.html
Desktop/Claude/launch-dq-site/launch-dq-v1-analysis-and-findings.md
Desktop/Claude/launch-dq-site/launch-dq-v2-gantt-ttv-context.md
Desktop/Claude/launch-dq-site/launch-dq-v3-skill-context.md
Desktop/Claude/launch-cases-brief.md
Desktop/Claude/dq-knowledge-base.md
Desktop/Accounts/closed-launch-analysis-2026-05-19.md
Desktop/Claude/dq-jeopardy-shortlist-v3.md
Desktop/Claude/dq-jeopardy-validation-report.md
Desktop/Claude/dq-jeopardy-handoff-FINAL.md
Desktop/Claude/skills/launch-time-to-value/
/Users/mattward/.pi/agent/skills/launch-time-to-value/
/Users/mattward/.pi/agent/skills/launch-inspector/SKILL.md
launch-inspector-data.json
.claude/cache/launch-inspector-summary-20260609T163231Z.md
```

### BigQuery tables referenced

```text
shopify-dw.sales.sales_opportunities
shopify-dw.sales.sales_launch_cases
shopify-dw.sales.sales_accounts
shopify-dw.sales.sales_churn_cases
shopify-dw.finance.shop_gmv_churn_daily_summary
shopify-dw.mart_revenue_data.revenue_reporting_launch_case_summary
shopify-dw.accounts_and_administration.shop_profile_current
shopify-dw.merchant_sales.b2b_orders_v2
shopify-dw.orders.orders_current
shopify-dw.mart_b2b_data.b2b_shop_gmv_enriched_daily
shopify-dw.mart_b2b_data.b2b_transactions__stage
```

---

## 19. Recommended next step

Build this as a reusable qualification module:

1. Start in SE Assistant / Opportunity Confidence / support automation surface; do not block on direct CRM integration.
2. Make VTP / MAP a first-class input.
3. Implement blocker modes before numeric TTV.
4. Port `model_v1.yaml` constants into a versioned config.
5. Add the above-the-fold Launch Confidence card.
6. Validate against the known calibration cases.
7. Capture actual outcomes and tune into v2.

If the teammate only has time for MVP, add these ten checks:

1. First-value metric missing → `Validate first`.
2. Complex B2B + ERP/middleware in scope + owner unknown → blocker.
3. No implementation owner + no partner → `Hold`.
4. Solo stakeholder + no DM + stale >60d → Pattern C blocker.
5. Hold/winback + stale >60d → Pattern A blocker.
6. Imported/draft/test activity counted as launch value → Pattern B blocker.
7. Existing B2B channel + no sunset plan late-stage/CW → Pattern D blocker.
8. D2C no launch intent / no response → block, no numeric TTV.
9. D2C scope pivot / out-of-scope partner requirements → timeline reset.
10. If no blocker, return ETTL/TTV band with confidence, missing inputs, next action, and reassessment TTL.
