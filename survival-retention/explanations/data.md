# Evenfall data dictionary

Extract date: 2026-10-31. All timestamps are UTC. Signups cover 2024-01-01 to 2026-09-30.

## subscribers.csv (one row per subscriber)
| column | description |
|---|---|
| subscriber_id | Unique subscriber key |
| signup_ts | When the free trial started |
| platform | Billing platform: iOS, Android, Web |
| plan | Plan chosen at trial start: monthly or annual |
| acquisition_channel | organic, app_store_ads, paid_social, paid_search, influencer, referral |
| country | Billing country (US, UK, CA, AU, DE, Other) |
| promo_code | Signup promo: BF_40 (Black Friday, 40% off first year), NY_20 (New Year, 20% off first year), blank if none |
| first_week_sessions | App sessions in the first 7 days |
| completed_onboarding | 1 if the user finished onboarding |

## subscription_events.csv (one row per event)
| column | description |
|---|---|
| event_id | Unique event key |
| subscriber_id | Joins to subscribers |
| event_ts | When the event happened |
| event_type | See below |
| amount_usd | Money moved (charges positive, refunds negative) |
| event_detail | Extra context, see below |

### event_type
| value | meaning |
|---|---|
| trial_start | 7-day free trial begins |
| trial_converted | First paid charge at trial end |
| renewal | Successful recurring charge |
| autorenew_off | User turned off auto-renew (access continues to period end) |
| autorenew_on | User turned auto-renew back on |
| payment_failed | A charge was declined; grace/dunning period starts |
| payment_recovered | A failed charge was successfully collected |
| expired | Access ended |
| refund | Money returned to the user |
| reactivated | A lapsed subscriber paid again |

### event_detail
- On `expired`: trial_expired, cancelled_by_user, billing_failure, refunded
- On `payment_failed`: trial_end or renewal
- On `reactivated`: organic or lifecycle_winback
- On `autorenew_off`: "trial" if it happened during the free trial

## Business context
- Free trial: 7 days, then billed at the plan price.
- Monthly: $12.99 (existing monthly subscribers before the June 2025 price change keep their old rate).
- Annual: $69.99.
- Grace/dunning window before access ends after a failed payment: iOS 16 days, Android 14 days, Web 10 days.