The dataset (4 files)

Scale: 106k users, 30 months (May 2024 to Oct 2026).
users: platform and trial start date.
events: the state-change log, about 286k rows.
bookings: every payment, about 357k rows.
engagement_monthly: engaged, casual or dormant per user-month.
Wrinkles: there are a few real-world quirks baked in, and we’ll find them in the checks. I’m not listing them up front.
Limitation: there is no legacy base. Everyone enters through a trial from May 2024, so early months look like a ramp. We’ll flag this in the technical docs.