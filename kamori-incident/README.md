# kamori-incident

A Claude skill for production incident investigation using [Kamori](https://kamori.io) logs.

## What it does

When you invoke this skill, Claude chains through five steps automatically:

1. **Anomaly detection** — checks if the current error rate is unusual vs the 7-day baseline
2. **Error distribution** — summarizes error counts by service and level
3. **Raw log inspection** — reads the most recent error lines from the affected service
4. **Distributed trace** — reconstructs the full request chain if a `trace_id` is found
5. **Blast radius** — produces a concrete error count for the last 30 minutes

The output is a structured **incident brief** with severity signal, affected services, root cause hypothesis, and recommended next steps.

## Example prompts

```
Something is wrong with the payments service
```
```
Error rate spiked — investigate the incident
```
```
We're getting alerts. Can you look at what's happening?
```

## Prerequisites

The Kamori MCP server must be connected to your Claude client. See [setup instructions](https://kamori.io/docs/mcp).

## Installation

```bash
# Install directly from this repository
claude skill install https://github.com/usekamori/kamori-skills/tree/main/kamori-incident

# Or copy the skill file manually
mkdir -p ~/.claude/skills/kamori-incident
curl -o ~/.claude/skills/kamori-incident/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-incident/SKILL.md
```

After installing, restart Claude Code or reload skills. The skill is automatically available — you never need to name it explicitly.
