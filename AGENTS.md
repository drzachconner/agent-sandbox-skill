# Agent Sandbox — Universal AI Agent Context

## What This Project Is

A skill and prompt library for managing isolated [E2B](https://e2b.dev/) sandbox environments from within any agentic coding tool (Claude Code, Codex CLI, Gemini CLI, Cursor, Copilot, etc.). Agents use this to safely execute code, scaffold and host full-stack apps, and run browser-based validation — all in a secure, ephemeral sandbox that never touches the local filesystem or production environment.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Sandbox runtime | E2B (`e2b` Python SDK) |
| CLI tool | Python 3.12+, `click`, `rich`, `uv` |
| Prompt templates | Markdown (`.md`) |
| Browser automation | Playwright (inside sandbox) |
| Default app stack (inside sandbox) | Vue + FastAPI + SQLite |

## Architecture

```
Agent-Sandbox/
├── .claude/skills/agent-sandboxes/
│   ├── SKILL.md              # Skill definition (capabilities, frontmatter)
│   ├── prompts/              # Workflow prompt templates (plan, build, host, test)
│   └── sandbox_cli/          # Python CLI — the core E2B wrapper
│       └── uv run sbx        # Entry point
├── prompts/
│   ├── full_stack/{claude,gemini,codex,sonnet,codex}/  # Difficulty-tiered app prompts
│   └── browser-workflows/    # Browser test prompt templates
├── CLAUDE.md                 # Claude Code-specific config
├── GEMINI.md                 # Gemini CLI-specific config
└── README.md                 # Full usage guide
```

## Key Backslash Commands (Universal)

These commands work by reading the prompt file and executing the corresponding skill workflow:

| Command | Description |
|---------|-------------|
| `\sandbox <prompt>` | Ad-hoc sandbox operation — minimal compute |
| `\agent-sandboxes:plan-full-stack <prompt>` | Generate an implementation plan |
| `\agent-sandboxes:build <plan_path>` | Execute a build plan inside a sandbox |
| `\agent-sandboxes:host <sandbox_id> <port>` | Expose a port and return a public URL |
| `\agent-sandboxes:test <sandbox_id> <url> <plan_path> <workflow_id>` | Validate a hosted app |
| `\agent-sandboxes:plan-build-host-test <prompt> <workflow_id>` | Full lifecycle: Plan → Build → Host → Test |

Command resolution: `\<cmd>` maps to `.claude/commands/<cmd>.md`; `\agent-sandboxes:<cmd>` maps to `.claude/skills/agent-sandboxes/prompts/<cmd>.md`.

## CLI Usage (Manual / Debugging)

```bash
# From .claude/skills/agent-sandboxes/sandbox_cli/
uv run sbx --help
uv run sbx init --timeout 1800
uv run sbx exec <sandbox_id> "echo hello"
uv run sbx files ls <sandbox_id> /home/user
uv run sbx browser start
```

## Setup

1. Copy `.env.sample` to `.env`
2. Add `E2B_API_KEY=sbx_...` (get from [E2B Dashboard](https://e2b.dev/dashboard/keys))
3. Requires Python >= 3.12 and `uv`

## Conventions

- Prompts are Markdown files — no code, just structured natural language instructions for agents
- Prompt filenames follow `{difficulty}_{app_name}.md` (e.g., `easy_todo_list.md`, `hard_freelancer_manager.md`)
- The CLI is the only Python code in this repo — everything else is prompts/config
- Do not store sandbox IDs or session state in files; sandboxes are ephemeral

## What NOT to Touch

- `.claude/skills/agent-sandboxes/sandbox_cli/` — the CLI is upstream code; local edits will be overwritten on skill updates
- `.env` — never commit; contains the E2B API key
- Prompt files in `prompts/full_stack/` — these are curated reference prompts; create new ones rather than modifying existing ones

## Related

- See `CLAUDE.md` for Claude Code-specific configuration
