# kamori-performance

A Claude skill for latency and performance investigation using [Kamori](https://kamori.io) logs.

## What it does

Investigates slow requests and high latency in your production system. Uses structured field queries and SQL to compute percentile breakdowns, then traces the slowest requests through your distributed system to find the bottleneck.

Steps:
1. **Find slow requests** — queries `body.ms > threshold` using `query_field`
2. **Percentile breakdown** — approximates P50/P90/P95/P99 from sorted query results
3. **Latency trend** — per-minute average latency over the investigation window
4. **Trace the slowest** — reconstructs the distributed trace for the worst-case request
5. **Error correlation** — checks whether slowness is accompanied by errors or timeouts

## Example prompts

```
Why is the API service slow?
```
```
Find requests taking longer than 2 seconds
```
```
P95 latency investigation for the last hour
```
```
Requests to /checkout are timing out — what's happening?
```

## Prerequisites

The Kamori MCP server must be connected to your Claude client. See [setup instructions](https://kamori.io/docs/mcp).

Your logs must include a numeric latency field in the JSON body (e.g. `ms`, `duration`, `elapsed`).

## Installation

```bash
claude skill install https://github.com/usekamori/kamori-skills/tree/main/kamori-performance

# Or manually:
mkdir -p ~/.claude/skills/kamori-performance
curl -o ~/.claude/skills/kamori-performance/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-performance/SKILL.md
```
