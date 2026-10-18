# Antler Industrial Supply: Account Dataset

**File:** `antler_industrial_accounts.csv` (12,000 accounts, one row per account, no missing values)
**Snapshot date:** 2026-08-31. Features describe the account as of that date. Targets cover 2026-09-01 to 2026-09-30.

## Identifiers and hierarchy

| Column | Description |
|---|---|
| `account_id` | Unique account ID (ANT-10001 to ANT-22000) |
| `industry_vertical` | Metal Fabrication, Machining & Auto Parts, Food & Beverage Processing, Plastics & Packaging, General Manufacturing, Construction & Contracting |
| `region` | Great Lakes, Northeast, Southeast, South Central, West |
| `assigned_rep_id` | REP-01 to REP-38. Each rep belongs to one region |

## Account profile

| Column | Description |
|---|---|
| `employee_band` | Customer headcount: 1-10, 11-50, 51-250, 251+ |
| `loyalty_tier` | Bronze, Silver, Gold, Platinum (based on trailing revenue) |
| `account_age_months` | Months since the first order |
| `consumables_revenue_share` | Share of lifetime revenue from consumables (gloves, fasteners, lubricants), 0 to 1 |

## Order history (RFM inputs)

| Column | Description |
|---|---|
| `days_since_last_order` | Recency. Accounts with no order in the last 12 months show 366 to 730 |
| `orders_12m` | Frequency. Orders in the trailing 12 months |
| `orders_last_90d` | Orders in the last 90 days |
| `revenue_12m` | Monetary. Trailing 12-month revenue in USD |
| `avg_order_value` | `revenue_12m / orders_12m` (0 if no orders) |
| `lifetime_orders` | Total orders since the account opened |

## Engagement (last 90 days)

| Column | Description |
|---|---|
| `email_open_rate_90d` | Share of marketing emails opened, 0 to 1 |
| `portal_logins_90d` | Web portal logins |
| `quote_requests_90d` | Quotes requested |
| `rep_contacts_90d` | Rep touches (calls, visits, emails) |

## Targets

| Column | Description |
|---|---|
| `ordered_next_30d` | 1 if the account placed at least one order in the 30 days after the snapshot. Main target |
| `next_30d_revenue` | Revenue from those orders in USD. Use for the expected value backtest only, never as a feature |

## Notes

- About 35% of accounts have fewer than 3 orders in the last 12 months. These are the thin-data accounts.
- Overall 30-day purchase rate is about 37%.
- `ground_truth_private.csv` holds the true purchase probability and the simulation parameters. Do not use it for modeling. It is only for checking calibration at the end.
- Regenerate with `generate_data.py` (seed 42).