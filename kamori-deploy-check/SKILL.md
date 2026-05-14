---
name: kamori-deploy-check
description: Run a post-deploy health check using Kamori logs. Compares error rates and log patterns before and after a deployment timestamp to produce a clear pass/warn verdict.
---

# Kamori Deploy Health Check

Use this skill immediately after deploying to production to confirm the deployment did not introduce regressions.

## When to invoke

- "We just deployed — can you check if everything looks okay?"
- "Run a deploy check for [service]"
- "Deployment health check"
- "Did the deploy break anything?"
- CI/CD post-deploy gate

## Required context

Ask the user for:
1. **Deploy timestamp** — when did the deploy complete? (e.g. "5 minutes ago", "14:32 UTC")
2. **Service name** — which service was deployed? (optional — defaults to all services)

If not provided, ask once before proceeding.

## Tool chain

### Step 1 — Error histogram around the deploy

```
histogram(bucket=5m, service=<deployed>, level=error, since=<30 min before deploy ISO>, until=<30 min after deploy ISO>)
```

Plot error counts in 5-minute buckets spanning 30 minutes before and after the deploy. Look for a step-change in error counts at or after the deploy timestamp.

### Step 2 — Before vs after counts

```
alert_summary(minutes=30, service=<deployed>, level=error)
```

Get the rolling 30-minute error count (post-deploy window).

Then compare against the pre-deploy baseline:

```
query_sql(sql="SELECT COUNT(*) AS pre_deploy_errors FROM logs WHERE service = '<service>' AND level = 'error' AND received_at >= '<deploy_time - 60min ISO>' AND received_at < '<deploy_time ISO>'")
```

This gives a concrete before/after number.

### Step 3 — New error patterns

```
search_logs(query="error OR exception OR fatal", service=<deployed>, since=<deploy time ISO>, limit=20)
```

Look for error messages that appeared after the deploy. Compare message text to understand if these are new patterns vs pre-existing noise.

### Step 4 — Warning-level signals

```
query_logs(service=<deployed>, level=warn, since=<deploy time ISO>, limit=10)
```

Check for new warnings that may precede a more serious failure.

### Step 5 — Anomaly check

```
anomaly_hint(service=<deployed>, level=error, window_minutes=15)
```

Confirm whether the post-deploy error rate is anomalous vs baseline.

## Output format

```
## Deploy Health Check — [service] @ [deploy timestamp]

**Verdict:** ✓ PASS  |  ⚠ WARN  |  ✗ FAIL

**Error counts:**
- Pre-deploy (60 min): [N]
- Post-deploy (30 min): [N]
- Change: [+N / -N / ~same]

**Anomaly signal:** [spike factor from anomaly_hint]

**New patterns detected:**
- [pattern 1, first seen at HH:MM]
- [pattern 2, first seen at HH:MM]
- None detected ✓

**Recommendation:**
[1–2 sentences: safe to proceed / monitor / rollback consideration]
```

**Verdict rules:**
- **PASS**: error count stable or lower, no new patterns, anomaly factor < 1.5
- **WARN**: error count up < 2×, or new patterns present but low volume
- **FAIL**: error count up > 2×, or anomaly factor > 3, or critical new stack traces

## Notes

- Compute all `since`/`until` values as ISO-8601 UTC strings relative to the deploy timestamp.
- "30 minutes ago" means `now - 30 minutes`, not a relative string — compute the actual UTC time.
- If the service has very low traffic (< 10 requests in the window), note the low sample size in the report.
