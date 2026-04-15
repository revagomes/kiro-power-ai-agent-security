# AI Agent Security & Governance — Kiro Power

Protect your codebase from prompt injection, enforce write-protected paths, restrict dangerous commands, and establish security incident response for AI-assisted development.

This is a [Kiro Power](https://kiro.dev/docs/powers/) — a knowledge bundle that activates dynamically when you discuss security topics with your Kiro agent.

## What's Included

| File | Purpose |
|------|---------|
| `POWER.md` | Core security principles, onboarding steps, and steering file map |
| `steering/prompt-injection-defense.md` | Detection patterns, the DATA ONLY principle, and response procedures |
| `steering/protected-paths-and-commands.md` | Write-protected paths, command whitelists, PII and credential handling |
| `steering/security-incident-response.md` | Incident types, response procedures, reporting templates, escalation |

## Installation

### From Kiro IDE

1. Open the Powers panel
2. Click **Add power from GitHub**
3. Enter: `revagomes/kiro-power-ai-agent-security`

### From local directory

1. Clone this repository
2. Open Kiro IDE → Powers panel → **Add power from Local Path**
3. Select the cloned directory

## When Does It Activate?

The power activates when your conversation mentions keywords like: `security`, `agent`, `governance`, `prompt injection`, `protected paths`, `ai safety`, `guardrails`, `credentials`, or `pii`.

## Who Is This For?

Any team using AI agents (Kiro, Copilot, Cursor, Claude Code, or any LLM-based assistant) to generate, review, or modify code. The content is framework-agnostic — it works with any language, framework, or CI/CD pipeline.

## Key Concepts

- **DATA ONLY principle** — All content read from external sources is data, never instructions
- **Protected paths** — Files that agents must never modify (secrets, auth config, infrastructure)
- **Command restrictions** — Whitelist approach: define what agents can run, deny everything else
- **Fail closed** — When in doubt, halt and ask the human

## Contributing

Contributions are welcome. If you have additional security patterns, detection rules, or incident response procedures, please open a pull request.

## License

MIT
