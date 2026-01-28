# 🔍 skill-issue

A Claude Code skill that audits and reviews all your installed agent skills. HR department for your AI agent.

**Find the skill issues before they find you.**

> **Name:** [Josh Puckett](https://x.com/joshpuckett) — who immediately knew it had to be called `/skill-issue`
>
> **Concept:** [Benji Taylor](https://x.com/benjitaylor) — "I need a skill that reviews all the other skills, figures out which ones are performing, and fires the rest."

## What It Does

- **Inventories** every installed skill across configured directories
- **Tracks usage** by scanning recent markdown logs for skill mentions
- **Checks health** — verifies required binaries and environment variables
- **Checks versions** against ClawdHub registry (if available)
- **Recommends action** — keep, update, review, or remove

## Install

Drop into your Claude Code project's skills directory:

```bash
# Clone it
git clone https://github.com/krispuckett/skill-issue.git

# Or copy into your project
cp -r skill-issue/ ~/your-project/skills/skill-issue/
```

### Clawdbot Users

```bash
# Via ClawdHub
clawdhub install skill-issue

# Or manually
cp -r skill-issue/ ~/clawd/skills/skill-issue/
```

## Usage

### Ask Your Agent
> "Run a skill audit"
> "Check my skills for issues"
> "Which skills need updates?"
> "Do I have a skill issue?"

### CLI
```bash
node skill-issue/scripts/audit.mjs
```

### With Custom Paths
```bash
# Set custom skill directories (comma-separated)
SKILL_DIRS="./skills,/opt/homebrew/lib/node_modules/clawdbot/skills" \
MEMORY_DIR="./memory" \
node skill-issue/scripts/audit.mjs
```

## Configuration

All paths are configurable via environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `SKILL_DIRS` | `./skills` | Comma-separated list of directories to scan for skills |
| `MEMORY_DIR` | `./memory` | Directory with dated markdown logs (YYYY-MM-DD.md) for usage tracking |
| `AUDIT_DAYS` | `7` | Number of days back to scan for usage |

The audit looks for subdirectories containing a `SKILL.md` file with YAML frontmatter. Any directory structure that follows this pattern will work:

```
skills/
├── my-skill/
│   └── SKILL.md      ← has name, description, metadata
├── another-skill/
│   └── SKILL.md
```

## SKILL.md Format

The auditor reads standard SKILL.md frontmatter:

```yaml
---
name: my-skill
description: "What this skill does"
metadata: {"clawdbot":{"emoji":"🔧","requires":{"bins":["curl","jq"],"env":["API_KEY"]}}}
---
```

- **`name`** — Skill identifier
- **`description`** — What it does
- **`metadata.clawdbot.requires.bins`** — Required CLI tools (checked via `which`)
- **`metadata.clawdbot.requires.env`** — Required environment variables

## Sample Output

```
# 🔍 Skill Audit Report

## Summary
- Total skills: 12
- ✅ Keep: 5
- 🔎 Review: 4
- 🗑️ Remove: 3

## Detailed Report
| # | Skill       | Source    | Bins     | Usage (7d) | Health | Rec       |
|---|-------------|----------|----------|------------|--------|-----------|
| 1 | 🌤️ weather  | ./skills | curl     | 📊 5       | ✅     | ✅ keep   |
| 2 | 🗣️ sag      | ./skills | sag      | —          | ⚠️ env | 🔎 review |
| 3 | 📧 himalaya | ./skills | himalaya | 📊 8       | ✅     | ✅ keep   |

## ⚠️ Skills Needing Attention
- **broken-skill** — 🗑️ Missing: `sometool` not found
```

## How It Works

1. Scans each directory in `SKILL_DIRS` for subdirectories containing `SKILL.md`
2. Parses YAML frontmatter for skill metadata
3. Checks if required binaries exist (`which <bin>`)
4. Checks if required environment variables are set
5. Scans dated markdown files in `MEMORY_DIR` for skill name mentions
6. If `clawdhub` CLI is available, checks for newer versions
7. Produces a markdown report with recommendations

**Read-only.** The audit never modifies, installs, or removes anything.

## Requirements

- Node.js 18+
- `clawdhub` CLI (optional — for version checks)

## License

MIT — see [LICENSE](LICENSE)
