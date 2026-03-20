# Samaritan

Templates, guides, and hard-won guardrails for running AI coding agents as development collaborators.

If you are building with [Claude Code](https://claude.ai/claude-code) and want to skip the part where you learn these lessons by losing data, deploying secrets, or exhausting your CI quota, start here.

## What's Inside

### [PROTOCOL.md](PROTOCOL.md)
Eight operating principles for working with AI agents responsibly. Each one exists because something broke without it.

### [docs/mobile-access.md](docs/mobile-access.md)
How to control your agents from your phone. Blink Shell + mosh + Tailscale + tmux. Survives sleep, wifi/cell switching, spotty signal. Full setup guide for Mac and Linux.

### [docs/galaxy-brain.md](docs/galaxy-brain.md)
How to write discussion answers that actually get marked on GitHub. Repo selection, question targeting, answer structure.

### [docs/anti-ai-policy-watchlist.md](docs/anti-ai-policy-watchlist.md)
Projects that ban or restrict AI contributions. Check before submitting.

### [templates/CLAUDE.md.template](templates/CLAUDE.md.template)
Starter instruction file for Claude Code with guardrails pre-configured.

### [memory/oss-targets.template.md](memory/oss-targets.template.md)
Tracking template for open source contribution work.

## What Goes Wrong

These are real incidents from production use:

1. **A chained bash command wiped ~1TB.** One semicolon, two commands, wrong target. Rule: one command per call, no chaining.
2. **Agent deployed client financial data to a public URL.** No concept of data classification. Rule: third-party data stays local or behind auth.
3. **Context compaction destroyed in-flight work.** Compressed context lost critical state. Rule: checkpoint every 45 minutes.
4. **Concurrent sessions overwrote each other's git changes.** No cross-session awareness. Rule: each session gets its own branch.
5. **Agent exhausted monthly CI quota in 18 pushes.** No model of finite resources. Rule: validate locally before pushing.

Every guardrail traces to an incident. The failures are the documentation.

## Architecture (Reference)

This is one working configuration. Adapt to what you have.

```
Phone (Blink Shell + mosh)
  |
  Tailscale (encrypted mesh, no port forwarding)
  |
  Dev Machine
  |--- VS Code + Claude Code (primary agent)
  |--- Parallel subagents (scout, review, research, code)
  |--- Docker (isolated worker agents, optional)
  |--- tmux (persistent sessions)
  |
  Cloud Server (optional)
  |--- Watchdog agent (health checks)
  |--- Scout agent (research, monitoring)
```

The minimum viable setup is Claude Code + a CLAUDE.md file + a memory directory. Everything else is optional and additive.

## Getting Started

1. Install [Claude Code](https://claude.ai/claude-code) (VS Code extension or CLI)
2. Copy [templates/CLAUDE.md.template](templates/CLAUDE.md.template) to your project root as `CLAUDE.md`
3. Create a `memory/` directory for persistent state
4. Read [PROTOCOL.md](PROTOCOL.md) before doing anything else

For mobile access, follow [docs/mobile-access.md](docs/mobile-access.md).

## Philosophy

AI agents are powerful local optimizers operating without global awareness. They will confidently do the wrong thing if the guardrails are not there. The system provides global awareness: rules, memory, oversight. The agents provide local execution: research, code, analysis. The human provides judgment.

The agent swarm is not autonomous. It is an instrument. The human plays it.

## License

MIT
