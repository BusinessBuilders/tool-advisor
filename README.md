<div align="center">

# 🧭 Tool Advisor

### A Claude Code plugin that finds the *right* tool for the job — from the toolkit you already have.

[![License](https://img.shields.io/badge/license-MIT-58A6FF?style=for-the-badge)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-plugin-D97757?style=for-the-badge&logo=anthropic&logoColor=white)](https://docs.claude.com/en/docs/claude-code)
[![Shell](https://img.shields.io/badge/scripts-Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)](scripts/)

You install dozens of plugins, skills, and agents — then forget half of them exist. **Tool Advisor scans your whole Claude Code toolkit, indexes what everything *does*, and recommends the best tool for whatever you're about to do** — proactively, mid-conversation, with a confidence score.

[Quick Start](#quick-start) · [Features](#features) · [Configuration](#configuration) · [How It Works](#how-it-works)

</div>

---

## Quick Start

```bash
/tool-advisor:scan-tools                       # index everything you have installed
/tool-advisor:recommend "review this pull request"   # get the best tool for a task
/tool-advisor:show-capabilities                # browse what you've got
```

## Overview

Tool Advisor helps you leverage your full Claude Code toolkit by:
- 🔍 Scanning and indexing all installed plugins, skills, and agents
- 🎯 Recommending the best tools for your tasks
- 💡 Proactively suggesting relevant tools during conversations
- 📚 Helping you discover capabilities you didn't know you had

## Features

### Commands

- **`/tool-advisor:recommend [task]`** - Get tool recommendations for a specific task
  ```bash
  /tool-advisor:recommend "deploy to production"
  /tool-advisor:recommend --task "code review" --prefer agent
  ```

- **`/tool-advisor:scan-tools`** - Scan and index all available tools
  ```bash
  /tool-advisor:scan-tools
  ```

- **`/tool-advisor:show-capabilities`** - List all available tools
  ```bash
  /tool-advisor:show-capabilities
  /tool-advisor:show-capabilities --all
  ```

### Agents

- **tool-scanner** - Automatically scans `.claude/` directories for capabilities
- **recommendation-engine** - Matches tasks to the best available tools
- **proactive-advisor** - Suggests relevant tools during conversations

### Skills

- **auto-optimize** - Automatically reads Project.md, asks clarifying questions, and spawns coordinated agents for project execution
  - Triggers: "read my Project.md and get started", "optimize my setup", "auto-optimize"
  - Use case: Project kickoff and initial setup with automated workflow orchestration

- **workflow-orchestrator** - Analyzes current conversation context and spawns agents mid-workflow
  - Triggers: "summarize and launch agents", "spawn agents for this work", "orchestrate the workflow"
  - Use case: Mid-project task delegation and agent coordination
  - Manual invoke: `Skill(skill="tool-advisor:workflow-orchestrator")`

- **discovery** - Techniques for discovering and analyzing installed tools
- **recommendation** - Algorithms for matching tasks to tools

### Hooks

- **pre-tool-call** - Checks for better specialized tools before execution
- **post-tool-call** - Suggests alternatives after tool execution
- **conversation-start** - Auto-scans tools when Claude starts
- **user-prompt-submit** - Proactively suggests tools based on user requests

## Installation

### Prerequisites

- Claude Code v1.0.0 or later
- Bash (for utility scripts)

### Install Plugin

```bash
# Plugin is already installed at:
~/.claude/plugins/tool-advisor/

# Verify installation
ls ~/.claude/plugins/tool-advisor/
```

## Configuration

Create `.claude/tool-advisor.local.md` in your project or home directory to customize behavior:

```yaml
# Enable/disable proactive suggestions
proactive_suggestions: true

# Auto-use tool if confidence exceeds this threshold
auto_use_threshold: 0.90

# Show suggestion if confidence exceeds this threshold
suggestion_threshold: 0.70

# Scan tools on conversation start
scan_on_start: true

# Scan frequency: hourly, daily, manual
scan_frequency: hourly

# Directories to scan (defaults shown)
scan_directories:
  - ~/.claude/plugins/
  - ~/.claude/skills/
  - ./.claude/

# Verbosity: quiet, normal, verbose
verbosity: normal

# Show match percentages in recommendations
show_scores: true

# Exclude plugins matching these patterns
exclude_plugins:
  - "legacy-*"
  - "deprecated-*"

# Preferred tools for specific task types
preferred_tools:
  deploy: "cloud-infrastructure"
  review: "code-review-ai"
  test: "tdd-workflows"
```

## Usage Examples

### Get Recommendations

```bash
# Simple recommendation
/tool-advisor:recommend "review this pull request"

# Structured recommendation
/tool-advisor:recommend --task "deploy to AWS" --prefer agent --exclude "legacy-*"
```

### Scan Your Tools

```bash
# Full scan with progress
/tool-advisor:scan-tools

# Quiet scan (updates cache silently)
/tool-advisor:scan-tools --quiet
```

### Browse Capabilities

```bash
# Grouped view
/tool-advisor:show-capabilities

# Full view with examples
/tool-advisor:show-capabilities --all
```

## How It Works

### 1. Discovery Phase

The tool-scanner agent scans:
- `~/.claude/plugins/` - User plugins
- `~/.claude/skills/` - User skills
- Project `.claude/` directory
- Connected MCP servers
- Built-in Claude Code tools

### 2. Indexing

Creates searchable index at `.claude/tool-advisor-cache.json` containing:
- Tool names and types
- Capabilities and descriptions
- Keyword mappings
- Usage metadata

### 3. Recommendation Engine

When you describe a task, the recommendation engine:
1. Analyzes your request (keywords, intent, context)
2. Searches the capability index
3. Scores matches using multi-factor algorithm:
   - Keyword match: 35%
   - Capability type: 25%
   - User history: 20%
   - Tool freshness: 10%
   - Success rate: 10%
4. Returns ranked recommendations

### 4. Proactive Suggestions

During conversations, the proactive-advisor agent:
- Monitors for task keywords (deploy, test, review, etc.)
- Detects question patterns ("how do I", "can you help")
- Checks error contexts for relevant tools
- Suggests tools inline: "💡 Using: [tool-name] (94% match)"

## Files and Structure

```
tool-advisor/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── README.md                # This file
├── skills/
│   ├── auto-optimize/
│   │   └── SKILL.md        # Project kickoff automation
│   ├── workflow-orchestrator/
│   │   └── SKILL.md        # Mid-workflow agent spawning
│   ├── discovery/
│   │   ├── SKILL.md        # Plugin discovery techniques
│   │   └── references/     # Detailed reference docs
│   └── recommendation/
│       └── SKILL.md        # Recommendation algorithms
├── agents/
│   ├── tool-scanner.md     # Scans .claude/ directories
│   ├── recommendation-engine.md
│   └── proactive-advisor.md
├── commands/
│   ├── recommend.md        # /tool-advisor:recommend
│   ├── scan-tools.md       # /tool-advisor:scan-tools
│   └── show-capabilities.md
├── hooks/
│   └── hooks.json          # Hook configurations
└── scripts/
    ├── scan-plugins.sh     # Discovers plugins
    ├── build-index.sh      # Creates search index
    └── parse-manifest.sh   # Extracts capabilities
```

## Troubleshooting

### Cache Issues

If recommendations seem stale:
```bash
/tool-advisor:scan-tools
```

### No Recommendations

Check that cache exists:
```bash
ls ~/.claude/tool-advisor-cache.json
```

### Disable Proactive Mode

Edit `.claude/tool-advisor.local.md`:
```yaml
proactive_suggestions: false
```

## Contributing

Contributions welcome! This plugin follows the Claude Code plugin development best practices.

## License

MIT License - See LICENSE file for details
