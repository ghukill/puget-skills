# puget-skills

Shared skills for [puget](https://github.com/your-org/puget), a minimal CLI coding agent.

Each top-level directory is a skill. Start with **[skill-guide](./skill-guide/)** — it explains the format, the conventions, and how to build your own.

## Available Skills

| Skill | Description |
|-------|-------------|
| [skill-guide](./skill-guide/) | Guide for authoring, installing, and managing puget skills |

## Quick Start

```bash
# Try a skill without installing
puget --skill ./skill-guide "help me create a new skill"

# Install to your project
puget skill install https://github.com/your-org/puget-skills/tree/main/skill-guide

# Install globally
puget skill install https://github.com/your-org/puget-skills/tree/main/skill-guide --global
```

## License

MIT
