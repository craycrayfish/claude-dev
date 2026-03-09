# claude-dev

A [Claude Code](https://code.claude.com) plugin for engineering work — TDD workflow, code review, continuous learning, and quality automation baked in.

## Installation

In Claude Code, access plugins using

```
/plugin
```

Then add the marketplace `craycrayfish/claude-dev`.

Or test locally during development:

```bash
claude --plugin-dir ./path/to/claude-dev
```

## What's included

### Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| `plan` | `/claude-dev:plan` | Restate requirements, assess risks, create a step-by-step plan — waits for your confirm before touching code |
| `tdd-dev` | `/claude-dev:tdd-dev` | Enforce TDD: write tests first, then implement, targeting 80%+ coverage |
| `code-review` | `/claude-dev:code-review` | Security and quality review of uncommitted changes — catches credentials, injection, and missing error handling |
| `test-coverage` | `/claude-dev:test-coverage` | Identify coverage gaps and generate missing tests (Jest, Vitest, pytest, Go, Rust, Java) |
| `refactor-clean` | `/claude-dev:refactor-clean` | Remove dead code using knip/depcheck/ts-prune/vulture with test verification |
| `checkpoint` | `/claude-dev:checkpoint` | Snapshot git state before risky changes; list or verify checkpoints |
| `update-codemaps` | `/claude-dev:update-codemaps` | Generate token-lean architecture docs in `docs/CODEMAPS/` |
| `update-docs` | `/claude-dev:update-docs` | Sync README/CONTRIBUTING/RUNBOOK from source-of-truth files |
| `model-route` | `/claude-dev:model-route` | Recommend haiku/sonnet/opus for a task based on complexity and budget |
| `orchestrate` | `/claude-dev:orchestrate` | Sequentially chain agents for complex multi-step tasks |
| `learn` | `/claude-dev:learn` | Extract reusable patterns from the current session into skills |
| `learn-eval` | `/claude-dev:learn-eval` | Like `learn`, but self-evaluates quality and picks the right save scope |
| `coding-standards` | `/claude-dev:coding-standards` | TS/JS/React/Node universal coding standards reference |
| `instinct-status` | `/claude-dev:instinct-status` | Show learned instincts (project + global) with confidence scores |
| `instinct-export` | `/claude-dev:instinct-export` | Export instincts to a file |
| `instinct-import` | `/claude-dev:instinct-import` | Import instincts from a file or URL |
| `promote` | `/claude-dev:promote` | Promote project-scoped instincts to global scope |
| `evolve` | `/claude-dev:evolve` | Analyze instincts and suggest or generate evolved structures |
| `projects` | `/claude-dev:projects` | List known projects and their instinct statistics |
| `continuous-learning-v2` | automatic | Observe sessions via hooks, create atomic instincts with confidence scoring, evolve into skills/commands/agents |

### Agents

| Agent | Description | Model |
|-------|-------------|-------|
| `codex-dev` | TDD specialist using Codex — write tests first, then implement | Sonnet |
| `architect` | System design and technical decision-making for complex architectural changes | Opus |
| `planner` | Planning specialist for features, architectural changes, and refactoring | Opus |
| `js-reviewer` (code-reviewer) | Code quality, security, and maintainability review | Sonnet |
| `py-reviewer` (python-reviewer) | Python-specific review: PEP 8, type hints, security, idioms | Sonnet |
| `security-reviewer` | OWASP Top 10, secrets, SSRF, injection, and unsafe crypto detection | Sonnet |
| `refactor-cleaner` | Dead code cleanup using static analysis tools | Sonnet |
| `doc-updater` | Generate and sync codemaps and documentation | Haiku |

### Hooks

Hooks run automatically — no setup needed.

| Event | Trigger | Behaviour |
|-------|---------|-----------|
| `PreToolUse: Bash` | Any bash command | Auto-start dev servers in tmux; remind to use tmux for long-running commands; prompt before `git push` |
| `PreToolUse: Write` | File write | Warn about non-standard documentation files |
| `PreToolUse: Edit\|Write` | File edits | Suggest manual compaction at logical intervals |
| `PreToolUse: *` | All tools | Capture observations for continuous learning (async) |
| `PostToolUse: Bash` | After bash | Log PR URL and provide review command after PR creation |
| `PostToolUse: Bash` | After bash | Async build analysis (background) |
| `PostToolUse: Edit\|Write\|MultiEdit` | After file edits | Run quality gate checks (async) |
| `PostToolUse: Edit` | After file edit | Auto-format JS/TS with Biome or Prettier |
| `PostToolUse: Edit` | After `.ts`/`.tsx` edit | TypeScript type-check |
| `PostToolUse: Edit` | After file edit | Warn about `console.log` statements |
| `PostToolUse: *` | All tools | Capture results for continuous learning (async) |
| `PreCompact` | Before compaction | Save session state |
| `SessionStart` | Session open | Load previous context and detect package manager |
| `Stop` | After each response | Check for `console.log` in modified files; persist session state; evaluate session for patterns; track token/cost metrics |
| `SessionEnd` | Session close | Session end lifecycle marker |

## Recommended workflow

```
New feature:   /plan → /tdd-dev → /code-review → /test-coverage
Bug fix:       /checkpoint → /tdd-dev → /code-review
Pre-ship:      /code-review → /test-coverage
After session: /learn-eval
Major refactor: /orchestrate
```

## Development

### Structure

```
claude-dev/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── agents/                   # Subagent definitions (.md with frontmatter)
├── skills/<name>/SKILL.md    # Agent skills
├── hooks/
│   └── hooks.json            # Event hooks
├── scripts/
│   └── hooks/                # Hook shell/node scripts (must be chmod +x)
├── settings.json             # Default plugin settings
├── CHANGELOG.md
└── README.md
```

### Testing locally

```bash
claude --plugin-dir .
```

### Adding a skill

1. Create `skills/<name>/SKILL.md` with frontmatter (`name`, `description`)
2. Optionally add `command: true` to expose it as `/claude-dev:<name>`
3. Test with `claude --plugin-dir .`

### Adding an agent

1. Create `agents/<name>.md` with frontmatter (`name`, `description`, `tools`, `model`)
2. The agent becomes available as `claude-dev:<name>` in the Agent tool

## License

MIT
