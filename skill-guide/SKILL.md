---
name: skill-guide
description: Guide for authoring, installing, and managing puget skills. Use when the user wants to create a new skill, understand the skill format, or contribute a skill to the puget-skills collection.
---

# Skill Guide

This skill teaches you how to author skills for [puget](https://github.com/your-org/puget), a minimal CLI coding agent.

## What is a Skill?

A skill is a self-contained capability package — a directory with a `SKILL.md` file that teaches the agent a specialized workflow. Skills are the primary extension mechanism for puget.

Puget's skill format follows the [Agent Skills](https://agentskills.io/home) open specification, making skills portable across compatible agent harnesses.

Only the **name** and **description** are injected into the system prompt at startup. The full skill content is loaded on demand (via `bash` with `cat`, or `read`) when the agent decides a task matches the description. This is progressive disclosure — keep context lean until it's needed.

## Skill Structure

```
my-skill/
├── SKILL.md              # Required — frontmatter + instructions
├── scripts/              # Optional — helper scripts the agent can run
│   └── do-thing.py
└── references/           # Optional — docs the agent can read on demand
    └── api-reference.md
```

The only required file is `SKILL.md`. Everything else is freeform.

## SKILL.md Format

```markdown
---
name: my-skill
description: One-line summary of what this does and when to use it. Be specific.
---

# My Skill

Instructions the agent follows when this skill is loaded.
```

### Frontmatter Rules

| Field         | Required | Notes |
|---------------|----------|-------|
| `name`        | Yes      | Lowercase `a-z`, `0-9`, hyphens. Must match the parent directory name. |
| `description` | Yes      | What the skill does **and when to use it**. Skills without a description are not loaded. |

The description is the most important field — it's what the agent sees in the system prompt to decide whether to load the skill. Be specific:

```yaml
# Good
description: Read web pages by fetching URLs and converting to readable markdown. Use when the user asks to read, summarize, or extract content from a website or URL.

# Bad
description: Helps with web stuff.
```

### Body Guidelines

- Write instructions as if talking to the agent — it will read and follow them literally.
- Use fenced code blocks for commands the agent should run.
- Use `SKILL_DIR` as a placeholder for the skill's directory path. The agent resolves this against the skill's `base_dir`.
- Reference helper scripts with relative paths from the skill directory.
- If a skill needs first-time setup, include a `## Setup` section.

## Trait Layers

Puget discovers skills from three locations, in priority order (first match wins):

| Layer   | Location                        | Scope |
|---------|---------------------------------|-------|
| Project | `.puget/skills/`                | Current project only |
| Global  | `~/.puget/skills/`              | All projects for this user |
| System  | `<puget package>/system/skills/`| Bundled with puget, read-only |

A project skill with the same name as a global skill shadows it. Ephemeral skills (`--skill` flag) shadow everything.

## Installing Skills

### From a local path

```bash
puget skill install ./path/to/my-skill            # → .puget/skills/
puget skill install ./path/to/my-skill --global    # → ~/.puget/skills/
```

### From a git URL

```bash
puget skill install https://github.com/user/repo
puget skill install https://github.com/user/repo/tree/main/skills/my-skill
puget skill install https://github.com/user/repo --ref v1.0 --path skills/my-skill
```

### Ephemeral (session only)

```bash
puget --skill ./path/to/my-skill "do the thing"
```

### Managing installed skills

```bash
puget skill list                    # All layers
puget skill list --project          # Project only
puget skill list --global           # Global only
puget skill remove my-skill         # Remove from project
puget skill remove my-skill -g      # Remove from global
```

## Contributing to puget-skills

This repository (`puget-skills`) is a collection of shared skills. Each top-level directory is one skill.

### To add a skill:

1. Create a directory named after your skill (lowercase, hyphens).
2. Add a `SKILL.md` with proper frontmatter.
3. Add any helper scripts or references.
4. Test it locally:
   ```bash
   puget --skill ./my-skill "test the skill"
   ```
5. Submit a PR.

### Conventions for this repo:

- **Directory name must match the `name` frontmatter field.**
- **Every skill must have a description.** No description = not discoverable.
- **Scripts should be self-contained.** Use inline dependency metadata (e.g., `uv run` with PEP 723 script metadata) so users don't need a separate install step.
- **Include a `## When to use this skill` section** so both humans and agents know the boundaries.
- **Include a `## When NOT to use this skill` section** to prevent misuse.

## Example: Minimal Skill

```
hello-world/
└── SKILL.md
```

```markdown
---
name: hello-world
description: Print a friendly greeting. Use when the user asks for a hello world demo.
---

# Hello World

When asked to greet the user, run:

\`\`\`bash
echo "Hello from puget! 🌊"
\`\`\`
```

## Example: Skill with Scripts

```
browser-read-url/
├── SKILL.md
└── scripts/
    └── fetch.py
```

The SKILL.md instructs the agent to run `uv run SKILL_DIR/scripts/fetch.py <url>`, and the script handles its own dependencies via inline metadata.
