# Samaritan

A multi-agent development system with human-in-the-loop oversight. Built on Claude Code, running across local and cloud infrastructure, controllable from your phone.

This is not a prompt. It is an architecture: persistent memory, parallel agents, mobile access, behavioral guardrails, and a human who stays in the loop at every decision point.

## What This Is

A working system for running AI coding agents as development collaborators. The agents research, write code, review PRs, draft content, manage infrastructure, and coordinate with each other. The human reviews, approves, directs, and catches what the agents miss.

The system has been in daily production use since late 2025 across a monorepo with 12+ projects, CI/CD pipelines, remote servers, and client work. It has produced [12 documented failure modes](https://github.com/vectara/awesome-agent-failures) along the way. Every guardrail in this system exists because something broke without it.

## Architecture

```
Phone (Blink Shell + mosh)
  |
  Tailscale (encrypted mesh, no port forwarding)
  |
  Mac Mini (primary)
  |--- VS Code + Claude Code (Tadao: primary agent, architect)
  |--- Parallel subagents (scout, review, research, code)
  |--- Docker (Riker: worker agent, heavy compute)
  |--- tmux (persistent sessions)
  |
  EC2 (remote)
  |--- Sentinel (host watchdog, health checks)
  |--- Egger (scout, monitor, research, morning briefs)
  |--- Cron jobs (health checks, usage tracking)
```

### The Agents

| Agent | Role | Location | Tools |
|-------|------|----------|-------|
| **Tadao** | Primary collaborator, architect | Mac Mini, VS Code | Full CLI, MCP servers, parallel subagents |
| **Egger-Cloud** | Scout, monitor, research | EC2 Docker container | Web search, GitHub, cron tasks |
| **Riker** | Worker, heavy compute | Mac Mini Docker container | Isolated code execution |
| **Sentinel** | Host watchdog | EC2 host | System monitoring, health checks |

### The Human

The human is not optional. The human is the system.

Every push requires explicit approval. Every commit requires explicit approval. Destructive commands require explicit approval. The agents build the infrastructure the human uses to oversee the agents. That is a structural conflict of interest, and it is documented and transparent.

## Key Components

### 1. Persistent Memory

Agents lose context between conversations. The memory system fixes this:

- **CLAUDE.md**: Behavioral rules, protocols, project-specific instructions. Loaded every session.
- **Memory files**: Persistent state organized by topic (projects, contacts, capabilities, feedback). Survives across conversations.
- **Thread protocol**: Per-session state files so concurrent conversations don't clobber each other.
- **Capabilities registry**: What the agent can and cannot do, proven with dates. Prevents capability amnesia.
- **Feedback files**: Corrections and confirmations from the human. The agent learns what works.

See [memory/](memory/) for templates.

### 2. Parallel Subagents

The primary agent can spawn specialized subagents that run concurrently:

- **Research agents**: Web search, documentation lookup, competitive analysis
- **Code review agents**: Pull diffs, analyze for bugs/security/edge cases
- **PR agents**: Clone repos, implement fixes, run tests, create pull requests
- **Scout agents**: Find opportunities, check statuses, monitor pipelines
- **Explorer agents**: Fast codebase search and navigation

A single directive ("research this and build that") can trigger 4-8 parallel agents, each returning results independently. The primary agent synthesizes and the human approves.

### 3. Mobile Access

Full CLI access from your phone. Survives sleep, wifi/cell switching, spotty signal.

```bash
# From anywhere
mosh macmini -- tmux a -t claude
```

**Stack**: Blink Shell (iOS) + mosh (UDP transport) + Tailscale (mesh VPN) + tmux (session persistence)

The human can review agent work, approve pushes, and redirect priorities from the car, the couch, or the grocery store. No laptop required.

See [docs/mobile-access.md](docs/mobile-access.md) for the full setup guide.

### 4. Behavioral Guardrails

Rules encoded in the agent's instruction file. Each one exists because something broke without it:

- **Never push without explicit approval.** The agent asks. The human says "ship it" or doesn't.
- **Never commit unless asked.** Prevents drive-by commits the human hasn't reviewed.
- **Never combine destructive commands.** One command, one purpose, one line. This rule exists because a malformed multi-command bash call wiped ~1TB of data.
- **Never run destructive operations on sole copies.** If no verified backup exists, refuse and flag it.
- **Checkpoint before context fills.** Save state to a file before the context window compacts, so nothing is lost.

See [PROTOCOL.md](PROTOCOL.md) for the full list with incident context.

### 5. MCP Servers

Model Context Protocol servers extend agent capabilities:

| Server | What It Does |
|--------|-------------|
| Gmail | Search and read email (read-only) |
| Google Calendar | Manage events, find free time |
| Blender | 3D scene control, procedural geometry |
| Ops | Infrastructure monitoring dashboard |
| Custom worker agents | Docker-based isolated execution |

### 6. Hooks

Shell commands that fire on agent events:

| Hook | Trigger | Purpose |
|------|---------|---------|
| PreCompact | Before context compaction | Saves git status + memory snapshot |
| PostCompact | After compaction | Restores context |
| PostToolUse | Every tool call | Tracks agent activity state |

## Use Cases

This system is general-purpose. Current workstreams:

- **Open source contributions**: Code review, pull requests, discussion answers
- **Blog/content pipeline**: Draft, edit, TTS narration, OG images, deploy
- **Infrastructure ops**: Server management, DNS, deploy pipelines, health monitoring
- **Project development**: Full-stack web apps (React, Vite, Three.js, Astro, FastAPI)
- **Research**: Web research, competitive analysis, documentation review
- **Client work**: Analysis, dashboards, reporting (with strict data classification rules)

## What Goes Wrong

This system has produced 12 documented failure modes. The highlights:

1. **Agent deployed client financial data to a public URL.** No concept of data classification.
2. **Agent confidently reported files as deleted when they existed.** Hallucination under search failure.
3. **Context compaction destroyed in-flight work.** Compressed context lost critical operational state.
4. **Concurrent sessions overwrote each other's git changes.** No cross-session file locking.
5. **Agent exhausted CI quota in 18 iterations.** No model of finite resources.

Every guardrail in this system traces back to one of these incidents. The failures are the documentation.

## Getting Started

### Minimum viable setup

1. [Claude Code](https://claude.ai/claude-code) (VS Code extension or CLI)
2. A CLAUDE.md file with your rules and protocols
3. A memory directory for persistent state

### Full setup (what we run)

1. Claude Max plan ($200/month) for Opus model + high rate limits
2. CLAUDE.md + memory system (see templates)
3. Tailscale + mosh + tmux for mobile access
4. EC2 instance for remote agents (optional)
5. Docker for isolated worker agents (optional)
6. MCP servers for extended capabilities (optional)

See [docs/](docs/) for setup guides.

## Philosophy

> "Systems do not break. The humans running them do."

AI agents are powerful local optimizers operating without global awareness. They will confidently do the wrong thing if you let them. The system provides the global awareness: tracking, rules, memory, oversight. The agents provide the local execution: research, code, analysis. The human provides judgment, taste, and accountability.

The agent swarm is not autonomous. It is an instrument. The human plays it.

## License

MIT

## Author

Travis Bonnet ([travisbreaks](https://github.com/travisbreaks))

Built with [Claude Code](https://claude.ai/claude-code) + Tadao (Claude Opus 4.6).
