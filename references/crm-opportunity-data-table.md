# CRM Opportunity Data Table Reference

Use this reference when running Opportunity Confidence and direct Salesforce tooling is unavailable or incomplete.

## Live Field Map

The live field catalog is available at:

```text
https://crm-fieldmap.quick.shopify.io/?tab=fields
```

That page may require Google authentication. If it is inaccessible from the agent runtime, use this bundled reference and state the access gap.

## Primary Table

```text
shopify-dw.sales.sales_opportunities
```

This table represents the current state of Salesforce opportunities in Shopify's data warehouse. It should be queried before concluding that opportunity-specific CRM data is unavailable.

## Query Strategy

Prefer exact lookup by Salesforce opportunity id:

```sql
SELECT *
FROM `shopify-dw.sales.sales_opportunities`
WHERE opportunity_id = @opportunity_id
LIMIT 5
```

If only a merchant or opportunity name is available, search by `name` and account/merchant fields visible in the live field map. Prefer open, recently updated, or most relevant opportunities. If multiple records are plausible, ask the user which opportunity to score.

## Recommended Fields

Request these fields when available:

| Field | Use |
| --- | --- |
| `opportunity_id` | Salesforce opportunity id / natural key. |
| `name` | Human-readable opportunity name. |
| `current_stage_name` | Current opportunity stage. |
| `opportunity_type` | New Business / Existing Business context. |
| `record_type` | Opportunity record type. |
| `primary_product_interest` | Product or solution focus. |
| `product_quantity` | Quantity of attached opportunity products. |
| `amount_usd` | Estimated total opportunity amount. |
| `expected_revenue_usd` | Expected revenue based on amount and probability. |
| `total_acv_amount_usd` | Annualized contract value for Plus products. |
| `total_revenue_usd` | Total lifetime revenue associated with the opportunity. |
| `incremental_annual_b2b_usd` | Annualized value created through B2B on Shopify. |
| `annual_online_revenue_verified_usd` | Verified annual online revenue. |
| `annual_total_revenue_verified_usd` | Verified total annual revenue. |
| `close_date` | Expected or actual opportunity close date. |
| `created_at` | Opportunity creation timestamp. |
| `updated_at` | Last opportunity update timestamp. |
| `qualified_date` | Date the opportunity was qualified. |
| `first_sales_activity_date` | First sales activity date. |
| `probability_of_closing` | Close probability percentage. |
| `forecast_category` | Forecast category tied to stage. |
| `next_step` | Next task or next step for closing. |
| `description` | Opportunity description. |
| `compelling_event` | Compelling event that caused or supports the opportunity. |
| `merchant_intent` | Indication of merchant commitment. |
| `primary_result_reason` | Primary won/lost reason, when present. |
| `secondary_result_reason` | Secondary won/lost reason, when present. |
| `reason_details` | Additional won/lost reason details. |
| `salesforce_account_id` | Associated Salesforce account id. |
| `salesforce_owner_id` | Owner user id. |
| `salesforce_owner_name` | Owner name. |
| `salesforce_owner_role` | Owner role. |
| `sales_qualifier` | User who qualified the opportunity. |
| `territory_name` | Salesforce territory. |
| `territory_region` | Territory region. |
| `territory_subregion` | Territory subregion. |
| `territory_line_of_business` | Territory line of business. |
| `territory_sales_motion` | Sales interaction / motion. |
| `territory_segment` | Territory segment. |
| `region` | Opportunity region. |
| `subregion` | Opportunity subregion. |
| `country_code` | Opportunity country code. |
| `source` | Inbound, outbound, or outreach source. |
| `lead_source` | Lead source that generated the opportunity. |
| `campaign_name` | Campaign associated with the opportunity. |
| `source_system` | Salesforce source system. |

## Scoring Mapping

Use CRM data as structured opportunity metadata:

| Dimension | CRM signals |
| --- | --- |
| Commercial Fit | Revenue/amount fields, product interest, opportunity type, merchant intent, segment, territory, source, campaign. |
| Technical Fit | Product interest, record type, description, reason details, line of business, sales motion. Always pair with SE-NTRAL/technical evidence. |
| Momentum | Stage, probability, forecast category, next step, close date, updated date, qualified date, first sales activity, compelling event, merchant intent. |
| Launch Confidence | Close date, next step, compelling event, owner/qualifier, description, product interest, sales motion. Pair with launch case, MAP, partner/SOW, and implementation owner evidence. |

## Guardrails

- CRM table data should reduce avoidable source gaps, but it is not a substitute for buyer-side communications.
- Do not infer stakeholder buy-in from CRM stage alone.
- Do not infer launch readiness from close date alone.
- If direct Salesforce MCP is unavailable but this table is queryable, do not say Salesforce data is inaccessible; say direct Salesforce was unavailable and CRM table data was used instead.
- If both direct Salesforce and CRM table lookup fail, mark the data gap explicitly and cap recommendations when the missing information could change the score.
