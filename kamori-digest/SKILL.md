---
name: kamori-digest
description: Generate a log digest summary for a time period — daily or weekly. Produces a prose narrative covering error trends, anomalies, top issues, and system health across all services.
---

# Kamori Log Digest

Use this skill to generate a structured health summary for a time period. Useful for standups, end-of-day reviews, weekly reports, or sharing system status with stakeholders.

## When to invoke

- "Give me a daily digest"
- "Weekly log summary"
- "What happened in the last 24 hours?"
- "System health report for this week"
- "Summarize logs for the standup"

## Required context

Ask the user for (if not specified):
1. **Period** — `day` (last 24h) or `week` (last 7 days). Default: `day`
2. **Services** — specific services, or all. Default: all

## Variables

Compute before running tools:
- `since` = now minus the period duration (ISO-8601 UTC)
- `until` = now (ISO-8601 UTC)
- `bucket` = `1h` for daily digest, `1d` for weekly digest

## Tool chain

### Step 1 — Service inventory

```
list_services()
```

Know which services are active. This determines scope.

### Step 2 — Error overview

```
summarize_errors(since=<since ISO>)
```

Get error and warn counts grouped by service. This is the backbone of the digest. Identify:
- Services with the highest error counts
- Services that appear for the first time (potential regression)
- Services with zero errors (healthy signal)

### Step 3 — Volume trend

```
histogram(bucket=<bucket>, since=<since ISO>, until=<until ISO>)
```

Plot total log volume over time. Identify:
- Traffic spikes
- Quiet periods (could indicate downtime or deploy windows)
- Trend direction (volume increasing or decreasing?)

For error-specific trend:

```
histogram(bucket=<bucket>, level=error, since=<since ISO>, until=<until ISO>)
```

### Step 4 — Anomaly check per service

For each service with significant error counts from step 2:

```
anomaly_hint(service=<name>, level=error, window_minutes=<1440 for day | 10080 for week>)
```

Note services with spike factor > 2.

### Step 5 — Top error messages

```
query_sql(sql="SELECT json_extract(body, '$.message') AS msg, COUNT(*) AS n FROM logs WHERE level = 'error' AND received_at >= '<since ISO>' GROUP BY msg ORDER BY n DESC LIMIT 10")
```

Find the most common error messages across all services. These are the "headlines" of the digest.

### Step 6 — Notable single events

```
query_logs(level=error, since=<since ISO>, limit=5)
```

Pull recent errors to see if any stand out as unique or severe (not covered by the top-10 aggregate above).

## Output format

Write the digest as a brief narrative report, not a raw data dump. Target: something a developer can read in 2 minutes.

```
## [Daily | Weekly] Log Digest — [date or date range]

### Summary
[2–3 sentences: overall system health, headline metric (total errors, spike factor), key callout]

### Service Health

| Service | Errors | Warns | Signal |
|---------|--------|-------|--------|
| [name]  | [N]    | [N]   | ✓ normal / ⚠ elevated / ✗ anomalous |
| ...     |        |       |        |

### Top Error Patterns
1. **"[error message]"** — [N] occurrences, [service]
2. **"[error message]"** — [N] occurrences, [service]
3. [up to 5 entries]

### Volume Trend
[1–2 sentences describing the histogram shape: steady, spike at HH:MM, quiet window, etc.]

### Anomalies
[List services with spike_factor > 2, or "No anomalies detected ✓"]

### Notable Events
[Any single events worth calling out — a new error type, a sudden burst, a recovery, etc.]
[Or: "Nothing unusual beyond the patterns above."]
```

## Notes

- Compute the `since` timestamp accurately. "Last 24 hours" = `now - 86400 seconds`. "Last 7 days" = `now - 604800 seconds`.
- Keep the narrative tight — executives and developers should both find it readable.
- If the period was quiet (< 10 total errors across all services), say so and keep the report short.
- `anomaly_hint` uses a 7-day baseline — for a weekly digest, the spike factor reflects the final day vs the prior 6 days.
