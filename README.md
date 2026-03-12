# skill-scout

> Discover, install, and run Claude Code skills — all in one step.

[한국어](README.ko.md)

**skill-scout** is a meta-skill for the Claude Code plugin ecosystem that automatically finds, installs, and runs skills you need.

It searches 6+ marketplaces with 70+ skills, plus GitHub and the web.

## Why?

Claude Code has a rich ecosystem of marketplaces and skills, but discovering and installing them manually is tedious. skill-scout reduces it to a single step.

```
"find a skill for PDF" → Search → Recommend → Install → Run
```

## How it works

```
User request
    │
    ▼
┌─────────────────────────────────┐
│  1. Search (3-tier parallel)    │
│  ├─ Local marketplaces (fast)   │
│  ├─ GitHub (fallback)           │
│  └─ Web search (last resort)    │
└──────────────┬──────────────────┘
               │
    ▼
┌─────────────────────────────────┐
│  2. Recommend                   │
│  - Relevance scoring & ranking  │
│  - Interactive selection table  │
└──────────────┬──────────────────┘
               │
    ▼
┌─────────────────────────────────┐
│  3. Install                     │
│  - Local copy / git clone /     │
│    web fetch → .omc/skills/     │
└──────────────┬──────────────────┘
               │
    ▼
┌─────────────────────────────────┐
│  4. Run                         │
│  - Auto-execute installed skill │
└─────────────────────────────────┘
```

## Supported sources

| Source | Method |
|---|---|
| [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | Local scan |
| [oh-my-claudecode](https://github.com/Yeachan-Heo/oh-my-claudecode) | Local scan |
| [claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | Local scan |
| [claude-code-plugins](https://github.com/anthropics/claude-code) | Local scan |
| [claude-code-workflows](https://github.com/wshobson/agents) | Local scan |
| [taskmaster](https://github.com/eyaltoledano/claude-task-master) | Local scan |
| GitHub repositories | API search |
| Web | Web search |

## Installation

```bash
claude plugin marketplace add ez2k/skill-scout
claude plugin install skill-scout
```

## Usage

### Slash command

```
/skill-scout:scout PDF
/skill-scout:scout code review
```

### Natural language

```
"find a skill for testing"
"need a skill for database migration"
"install skill for code review"
```

## Project structure

```
skill-scout/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
└── skills/
    └── scout/
        └── SKILL.md         # Skill definition
```

## License

MIT
