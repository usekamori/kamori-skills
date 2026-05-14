---
name: kamori-incident
description: Investigate a live or recent incident using Kamori logs. Chains anomaly detection, error summarization, raw log inspection, and distributed trace reconstruction into a structured incident brief.
---

# Kamori Incident Investigation

Use this skill when responding to a production incident, an alert, or any "something is broken right now" moment.

## When to invoke

- "Something is wrong with [service]"
- "We're getting alerts — can you investigate?"
- "Error rate spiked — what happened?"
- "Investigate the incident"
- Any oncall / PagerDuty scenario

## Tool chain

Run these tools in order. Each step informs the next.

### Step 1 — Anomaly detection

```
anomaly_hint(service?, level="error", window_minutes=60)
```

Establish whether the current error rate is abnormal vs the 7-day baseline. Report the spike factor. If spike factor < 1.5, note that the rate appears normal and ask the user whether to continue.

### Step 2 — Error distribution

```
summarize_errors(since=<1 hour ago ISO>)
```

Show counts grouped by `service` + `level`. Identify which service and level are dominating. If the user already named a service, still run this to confirm the blast radius.

### Step 3 — Raw log inspection

```
query_logs(service=<top offender>, level=error, since=<30 min ago ISO>, limit=20)
```

Pull the most recent error lines for the top-offending service. Read the `body` field carefully. Look for:
- Stack traces
- Error message patterns
- `trace_id` values
- Timestamps clustering (is it continuous or a single burst?)

If multiple services are spiking, run `query_logs` for each.

### Step 4 — Distributed trace (if trace_id found)

```
trace_logs(trace_id=<id from step 3>)
```

Reconstruct the full request chain. Identify where in the chain the error originates. Note which services are downstream vs upstream of the failure point.

### Step 5 — Full event detail (if needed)

```
get_log(id=<interesting row id>)
```

Fetch the complete JSON body for the most informative error row found in step 3.

### Step 6 — Scope confirmation

```
alert_summary(minutes=30, service=<affected>, level=error)
```

Get a concrete count for the last 30 minutes. Use this number in the incident brief.

## Output format

Produce a structured incident brief:

```
## Incident Brief — [timestamp]

**Severity signal:** [spike factor from anomaly_hint, e.g. "4.2× baseline — likely anomaly"]
**Affected services:** [list]
**Error count (last 30 min):** [number from alert_summary]

**Root cause hypothesis:**
[1–3 sentences based on the log content]

**Evidence:**
- Log id [N]: [key excerpt from body]
- Trace [id]: [what the trace shows]
- Pattern: [continuous / burst at HH:MM / isolated]

**Recommended next steps:**
1. [specific action]
2. [specific action]
```

If the investigation is inconclusive (not enough logs, unfamiliar error pattern), say so explicitly and suggest what additional context would help.

## Notes

- Always use ISO-8601 UTC timestamps (e.g. `2024-06-01T12:00:00Z`). Compute `since` relative to the current time.
- Do not fabricate log content. Quote directly from `body` fields.
- If `list_services` is needed to discover service names, call it before step 1.
