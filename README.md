# kamori-skills

Claude skills for developers using [Kamori](https://kamori.io) — the structured log platform for AI-native teams.

These skills give Claude step-by-step workflows for common developer moments: investigating incidents, checking deploys, debugging errors, monitoring in real time, profiling performance, and generating digests. Install them once; invoke them with plain English.

## Skills

| Skill | What it does | Example prompt |
|-------|-------------|----------------|
| [kamori-incident](./kamori-incident/) | Incident triage and investigation | "Something is wrong with payments" |
| [kamori-deploy-check](./kamori-deploy-check/) | Post-deploy health check | "Did the deploy break anything?" |
| [kamori-debug-error](./kamori-debug-error/) | Debug a specific error or exception | "I'm seeing: connection refused" |
| [kamori-monitor](./kamori-monitor/) | Live log tail with anomaly alerting | "Watch auth for errors" |
| [kamori-performance](./kamori-performance/) | Latency and slow-request investigation | "Why is the API slow?" |
| [kamori-digest](./kamori-digest/) | Daily or weekly log health digest | "Weekly log summary" |

## Prerequisites

1. A running Kamori instance ([self-hosted](https://kamori.io/docs/self-hosting) or [cloud](https://kamori.io))
2. The Kamori MCP server connected to your Claude client — see [MCP setup](https://kamori.io/docs/mcp)
3. Claude Code, Claude Desktop, or Cursor with MCP support

## Installation

### Install all skills

```bash
# Clone the repository
git clone https://github.com/usekamori/kamori-skills.git
cd kamori-skills

# Copy all skills to your Claude skills directory
cp -r kamori-* ~/.claude/skills/
```

### Install a single skill

```bash
mkdir -p ~/.claude/skills/kamori-incident
curl -o ~/.claude/skills/kamori-incident/SKILL.md \
  https://raw.githubusercontent.com/usekamori/kamori-skills/main/kamori-incident/SKILL.md
```

Replace `kamori-incident` with any skill name:
- `kamori-deploy-check`
- `kamori-debug-error`
- `kamori-monitor`
- `kamori-performance`
- `kamori-digest`

### Verify installation

After copying, restart Claude Code or run:

```
/skills
```

You should see the `kamori-*` skills listed. You never need to name them explicitly — Claude picks up the right skill based on your prompt.

## How skills work

Each skill is a `SKILL.md` file placed in `~/.claude/skills/<skill-name>/`. When you describe a task that matches a skill's intent, Claude automatically follows the workflow defined in that skill — calling the right Kamori MCP tools in the right order and formatting the output consistently.

Skills are plain Markdown. You can read, fork, or customize them for your team.

## MCP Tools used

These skills use the following Kamori MCP tools:

`anomaly_hint` · `summarize_errors` · `query_logs` · `trace_logs` · `get_log` · `alert_summary` · `histogram` · `search_logs` · `query_field` · `query_sql` · `list_services` · `tail_logs` · `watch_logs`

Full tool documentation: [kamori.io/docs/mcp](https://kamori.io/docs/mcp)

## Contributing

Issues and pull requests welcome. Each skill lives in its own directory — changes to one skill don't affect others.

## License

MIT
