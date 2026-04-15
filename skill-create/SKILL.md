---
name: skill-create
description: >
  Create, format, validate, and migrate Claude Code skills using the official
  directory-based SKILL.md format with YAML frontmatter. Use when creating new
  skills, migrating old flat-file commands, reformatting existing skills, or
  validating skill structure against current standards.
allowed-tools: Read Write Bash Glob Grep Edit
---

# Skill Create

> **Format knowledge version**: 2026-04-15
> Based on: code.claude.com/docs/en/skills.md, agentskills.io/specification
> If this skill is more than a few months old, verify frontmatter fields against
> current docs before relying on it — the format may have evolved.

Create, validate, and migrate Claude Code skills to the current official format.

## Directory Structure

Every skill is a folder containing `SKILL.md` plus optional supporting files:

```
skill-name/
├── SKILL.md             # Required: frontmatter + instructions
├── references/          # Optional: detailed docs
│   └── REFERENCE.md
├── scripts/             # Optional: helper scripts
│   └── helper.sh
└── assets/              # Optional: templates, resources
    └── template.txt
```

### Storage Locations (priority order)

1. **Enterprise** — managed settings (org-wide)
2. **Personal** — `~/.claude/skills/<name>/SKILL.md` (all projects)
3. **Project** — `.claude/skills/<name>/SKILL.md` (this repo only)
4. **Plugin** — `<plugin>/skills/<name>/SKILL.md` (namespaced as `plugin:name`)

## YAML Frontmatter Reference

Every `SKILL.md` must start with YAML frontmatter between `---` delimiters.

### Core Fields (Agent Skills Standard)

```yaml
---
name: my-skill                 # Required. Lowercase, hyphens only, max 64 chars
description: >                 # Required. 1-1024 chars. What it does + when to trigger
  Concise action-oriented description with natural trigger terms.
  Front-load the key use case.
allowed-tools: Read Write Bash # Optional. Space-separated. Pre-approved tools
license: MIT                   # Optional. License name or path
compatibility: git, Node 20+  # Optional. Environment requirements, max 500 chars
metadata:                      # Optional. Custom key-value pairs
  author: your-name
  version: "1.0"
---
```

### Claude Code-Specific Fields

```yaml
---
# Invocation control
disable-model-invocation: false  # true = user-only (/skill-name), Claude won't auto-trigger
user-invocable: true             # false = hidden from / menu, Claude auto-triggers only
when_to_use: >                   # Additional trigger phrases (appended to description)
  Extra trigger context or examples of when this skill should activate.

# Parameters
argument-hint: "[issue-number]"  # Shown in autocomplete

# Execution
model: session                   # Override model for this skill
effort: session                  # low / medium / high / max
context: inline                  # "fork" runs in isolated subagent
agent: general-purpose           # Subagent type when context: fork
                                 # Options: general-purpose, Explore, Plan, or custom
shell: bash                      # Shell for !`command` injection (bash or powershell)

# Scoping
paths:                           # Glob patterns — only auto-activate in matching paths
  - "packages/frontend/**"
hooks: {}                        # Hooks scoped to skill lifecycle
---
```

### Invocation Control Matrix

| Setting | User invokes via `/` | Claude auto-invokes |
|---------|---------------------|-------------------|
| Default (both true) | Yes | Yes |
| `disable-model-invocation: true` | Yes | No |
| `user-invocable: false` | No | Yes |

Use `disable-model-invocation: true` for risky workflows (`/deploy`, `/commit`).
Use `user-invocable: false` for background knowledge skills.

### String Substitutions (available in body)

| Variable | Expands to |
|----------|------------|
| `$ARGUMENTS` | All arguments passed on invocation |
| `$ARGUMENTS[N]` or `$N` | Nth positional arg (0-based) |
| `${CLAUDE_SESSION_ID}` | Current session ID |
| `${CLAUDE_SKILL_DIR}` | Directory containing this SKILL.md |

### Dynamic Context Injection

Use `` !`command` `` to run shell commands before Claude sees the skill content:

```markdown
## Current branch context
- Git status: !`git status --short`
- Recent commits: !`git log --oneline -5`
```

The output replaces the `` !`command` `` inline. Useful for injecting live state.

## Creating a New Skill

### Step 1 — Plan

Answer these before writing:
- **What capability?** Keep it focused — one topic per skill
- **Who triggers it?** User via `/`, Claude automatically, or both?
- **What trigger terms?** Include in `description` — verbs users will naturally say
- **What tools?** Restrict with `allowed-tools` where possible
- **Supporting files needed?** Keep SKILL.md under 500 lines; split detail into references

### Step 2 — Create

```bash
mkdir -p ~/.claude/skills/my-skill
```

### Step 3 — Write SKILL.md

Start with frontmatter, then concise imperative instructions.

**Description best practices:**
- Include action verbs: "create", "debug", "migrate", "deploy"
- Mention specific technologies by name
- Use natural phrasing: "when choosing between X and Y"
- Avoid vague terms: "helps with testing" is bad; "debug failing tests, choose test framework" is good

**Body best practices:**
- Use imperative form ("Extract text...", "Run the build...")
- Explain the "why" behind instructions, not just rigid ALWAYS/NEVER rules
- Include 2-3 realistic examples per task
- Keep under 500 lines — move detail to `references/REFERENCE.md`
- Include "ultrathink" in body to enable extended thinking

### Step 4 — Test activation

Ask Claude questions matching your trigger terms. If it doesn't activate:
- Add more specific terms to `description` or `when_to_use`
- Make trigger phrases more natural
- Use `/skill-creator` to evaluate and benchmark

## Migrating Flat-File Commands

For old `.claude/commands/skill-name.md` or `.claude/skills/skill-name.md` files:

1. **Create directory**: `mkdir -p .claude/skills/skill-name`
2. **Read existing content** and extract: name, description, tools used, trigger conditions
3. **Create `SKILL.md`** with frontmatter + original content (update structure to match above)
4. **Update cross-references**: `skill.md` becomes `../skill/SKILL.md`
5. **Verify** the new skill activates correctly
6. **Remove** the old flat file only after confirming

## Validation Checklist

When creating or reviewing a skill, verify:

- [ ] Directory exists: `<skills-dir>/skill-name/`
- [ ] File named exactly `SKILL.md`
- [ ] YAML frontmatter present with `---` delimiters (no tabs, spaces only)
- [ ] `name` is lowercase-with-hyphens, max 64 chars, matches directory name
- [ ] `description` is 1-1024 chars with specific trigger terms
- [ ] `allowed-tools` uses space-separated format (not comma-separated)
- [ ] Body is under 500 lines; detail moved to supporting files
- [ ] No outdated or invalid frontmatter fields
- [ ] Cross-references use correct relative paths
- [ ] Skill activates when tested with relevant prompts

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Skill won't load | Check YAML: proper `---` delimiters, no tabs, valid fields |
| Claude ignores skill | Add specific trigger terms to `description` / `when_to_use` |
| "Tool not allowed" error | Add missing tool to `allowed-tools` (space-separated) |
| Cross-references broken | Update: `skill.md` → `../skill/SKILL.md` |
| New skill not discovered | New top-level skills directory requires session restart |
| Skill too long | Move detail to `references/REFERENCE.md`, keep SKILL.md under 500 lines |
