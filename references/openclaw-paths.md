# OpenClaw paths and CLI (quick reference)

Use this when turning a finalized plan into real files and config.

## Canonical locations

- Global config: `~/.openclaw/openclaw.json` (override with `OPENCLAW_CONFIG_PATH`)
- State / sessions: `~/.openclaw` (`OPENCLAW_STATE_DIR`)
- Default workspace: `~/.openclaw/workspace` (or `agent.workspace` / per-profile `workspace-<profile>`)
- Per-agent state: `~/.openclaw/agents/<agentId>/agent` and `.../sessions`
- Shared skills: `~/.openclaw/skills`
- Workspace skills (per agent): `<workspace>/skills/<skill-name>/SKILL.md`

## Workspace files (root of each agent workspace)

| File | Role |
|------|------|
| `AGENTS.md` | Operating rules, session startup, red lines |
| `SOUL.md` | Persona, tone, boundaries |
| `USER.md` | Who the operator is (loaded every session) |
| `IDENTITY.md` | Name, vibe, emoji (also sync to config via CLI) |
| `TOOLS.md` | Local tool notes only — does **not** enable/disable tools |
| `HEARTBEAT.md` | Short checklist for heartbeat polls; keep tiny |
| `MEMORY.md` | Optional long-term memory (main session only) |

## Multi-agent

- Add agent (non-interactive): `openclaw agents add <id> --workspace <abs-path> --non-interactive`
- Bind routing: `openclaw agents bind --agent <id> --bind telegram:ops`
- Sync identity from file: `openclaw agents set-identity --agent <id> --from-identity`
- List: `openclaw agents list --bindings`

`main` is reserved; use another id for new agents.

## Docs (human)

- Agent workspace: https://docs.openclaw.ai/concepts/agent-workspace
- Multi-agent: https://docs.openclaw.ai/concepts/multi-agent
- CLI agents: https://docs.openclaw.ai/cli/agents

## Repo templates (for style parity)

- `docs/reference/templates/AGENTS.md`
- English concept page: `docs/concepts/agent.md`

## 对话与举例（本 skill）

- `references/conversation-patterns.md` — 问答模板、OpenClaw 术语人话版、垂直领域流程草稿法
- `references/external-skills.md` — 用 **find-skills** + `npx skills find` 检索真实生态包，禁止臆造；同质化结果择优

## 生态 Skills（安装型）

- 检索与安装流程：使用 **`find-skills`** skill（`npx skills find`、`npx skills add`）
- 目录站：https://skills.sh/
