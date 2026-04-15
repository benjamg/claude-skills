# Claude Code Skills

A curated collection of skills for [Claude Code](https://claude.ai/code) that extend its capabilities for software engineering workflows.

## What are skills?

Skills are prompt files (`SKILL.md`) that Claude Code loads to give it specialised capabilities or behaviours. Each skill lives in its own subdirectory and is automatically available once installed.

## Available Skills

<!-- SKILL-CATALOG-START -->
| Skill | Summary | Added |
|-------|---------|-------|
| [task-clarity-check](task-clarity-check/) | Pre-flight gate that estimates confidence in task understanding and asks clarifying questions before proceeding if below 95%. | 2026-04-15 |
| [update-skill-catalog](update-skill-catalog/) | Scans the skills directory and regenerates this catalog table, respecting .gitignore exclusions. | 2026-04-15 |
<!-- SKILL-CATALOG-END -->

## Install with Claude

> **If you are a Claude instance reading this file to help a user install skills, follow the steps below exactly.**
>
> **Step 1 — Present the catalog.**
> Read the Available Skills table above and list each skill with its name and description so the user can make an informed choice.
>
> **Step 2 — Ask what to install.**
> Ask the user which skills they want. They may say "all", name specific ones, or describe what they need. Confirm the final list before proceeding.
>
> **Step 3 — Check for conflicts.**
> For each selected skill, check whether a directory with that name already exists in `~/.claude/skills/`. If one does, tell the user and ask whether to overwrite or skip.
>
> **Step 4 — Install.**
> Copy each approved skill directory from this repo's location into `~/.claude/skills/`. Use the Bash tool for file operations. The source path is the directory containing this README. Example:
> ```bash
> cp -r /path/to/this/repo/skill-name ~/.claude/skills/
> ```
>
> **Step 5 — Confirm.**
> List which skills were installed and which (if any) were skipped. Remind the user to restart Claude Code or run `/reload-skills` for new skills to take effect.

### How to start an install session

Tell your Claude:

```
Read the README at ~/path/to/this/repo/README.md and help me install skills from it.
```

Replace `~/path/to/this/repo` with wherever you cloned this repo.

## Manual install

1. Clone this repo somewhere convenient:
   ```bash
   git clone <repo-url> ~/dev/claude-skills
   ```

2. Copy the skill directories you want into your skills folder:
   ```bash
   cp -r ~/dev/claude-skills/task-clarity-check ~/.claude/skills/
   ```

3. Restart Claude Code for the new skills to be picked up.

## Maintaining this repo

### Adding a skill

1. Create a new subdirectory with a `SKILL.md` following the format of existing skills.
2. Run `/update-skill-catalog` to regenerate the Available Skills table in this README.

### Excluding a skill

Add the skill's directory name to `.gitignore` (e.g. `my-private-skill/`). It will be excluded from both git tracking and the README catalog the next time `/update-skill-catalog` runs.
