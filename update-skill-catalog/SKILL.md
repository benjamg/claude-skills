---
name: update-skill-catalog
description: >
  Scans the skills directory, reads each SKILL.md, respects .gitignore exclusions,
  and regenerates the Available Skills table in README.md.
  TRIGGER when: user runs /update-skill-catalog or asks to refresh/rebuild/update
  the skills catalog or README. Do NOT trigger for general file edits.
---

# Update Skill Catalog

Regenerate the Available Skills table in `~/.claude/skills/README.md` by scanning
the skills directory and respecting `.gitignore` exclusions.

## Steps

### 1 — Resolve the skills directory

The skills directory is `~/.claude/skills/`. Expand `~` to the user's actual home
directory path for all subsequent file operations.

### 2 — Read .gitignore and extract excluded directories

Read `~/.claude/skills/.gitignore`. Parse every non-comment, non-blank line.
Extract directory names to exclude by:
- Taking lines that end with `/` — strip the trailing slash to get the directory name
- Taking lines that look like bare directory names (no `.` extension, no `*` wildcard)
- Ignoring lines starting with `#`, blank lines, OS/editor patterns (`.DS_Store`,
  `*.swp`, `.vscode/`, `.idea/`, `Thumbs.db`, etc.)

Build a set of **excluded directory names**.

### 3 — Scan for skill directories

List all immediate subdirectories of `~/.claude/skills/`. For each subdirectory:
- Skip it if its name is in the excluded set from Step 2
- Skip it if it does not contain a `SKILL.md` file
- Otherwise, add it to the list of skills to catalog

### 4 — Extract skill metadata

For each skill directory that passed Step 3, read its `SKILL.md` file. Parse the
YAML frontmatter between the `---` delimiters. Extract:
- `name` — the skill's display name (fall back to the directory name if absent)
- `description` — the full description text (collapse multiline `>` blocks into a
  single line by joining with a space and trimming whitespace)

Truncate the description to a readable length (aim for under 200 characters) by
cutting at a sentence boundary if possible. Do not truncate mid-word.

### 5 — Sort the skills

Sort the skill entries alphabetically by directory name, case-insensitive.

### 6 — Build the new catalog table

Construct a markdown table with this exact format:

```
| Skill | Summary | Added |
|-------|---------|-------|
| [skill-name](skill-directory-name/) | Brief description text here. | YYYY-MM-DD |
```

Each row links the skill name to its subdirectory using a relative markdown link.

The **Added** column is the date the skill was first cataloged. When rebuilding:
- If the skill already has a row in the existing table, preserve its original Added date.
- If the skill is new (not in the current table), use today's date.

### 7 — Replace the catalog section in README.md

Read `~/.claude/skills/README.md`. Find the two marker comments:
```
<!-- SKILL-CATALOG-START -->
<!-- SKILL-CATALOG-END -->
```

Replace everything between (and including) those two markers with:
```
<!-- SKILL-CATALOG-START -->
[new table here]
<!-- SKILL-CATALOG-END -->
```

Write the updated content back to `~/.claude/skills/README.md`.

### 8 — Report

Tell the user:
- How many skills were cataloged
- How many were skipped (excluded by .gitignore or missing SKILL.md)
- That README.md has been updated
