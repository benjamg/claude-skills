---
name: task-clarity-check
description: >
  Pre-flight task analysis gate. Before planning, coding, or spawning agents for any
  non-trivial task, assess your confidence that you fully understand the requirements.
  If confidence is below 95%, ask targeted clarifying questions before proceeding.
  TRIGGER when: user gives a new implementation task, feature request, bug fix,
  refactor, or multi-step engineering request. Do NOT trigger for simple questions,
  file reads, or single-line fixes.
---

# Task Clarity Check

You MUST run this analysis internally before beginning any non-trivial task.
Do NOT skip ahead to planning, coding, or spawning agents until this gate passes.

## Step 1 — Decompose the request

Break the user's request into its constituent parts:

- **Goal**: What is the desired end state?
- **Scope**: What files, systems, or areas are affected?
- **Constraints**: Are there explicit requirements on approach, tech, or style?
- **Acceptance criteria**: How will you know the task is done correctly?
- **Dependencies**: Does this depend on external context you don't have?

## Step 2 — Identify ambiguities

For each part above, flag anything that is:

- Unclear or could be interpreted multiple ways
- Missing entirely (e.g. no acceptance criteria stated)
- Assumed by you but not confirmed by the user
- Dependent on codebase knowledge you haven't yet verified

## Step 3 — Estimate confidence

Rate your confidence (0-100%) that you can execute this task correctly
on the first attempt without needing to come back with questions mid-way.

**Factors that lower confidence:**
- Vague or ambiguous requirements
- Multiple valid interpretations of what "done" looks like
- Unknown target files, APIs, or architecture
- Missing context about business logic or user intent
- Unclear edge case handling
- No stated preference when multiple approaches exist

**Factors that raise confidence:**
- Specific file paths, function names, or line numbers mentioned
- Clear before/after description
- Explicit constraints on approach
- Small, well-defined scope
- You've already read the relevant code in this session

## Step 4 — Act on the result

### If confidence >= 95%

State your understanding back to the user in 2-3 concise bullet points, then proceed
with planning or execution. Keep it brief — don't over-explain what's obvious.

Format:
```
**Task understanding** (confidence: X%)
- [bullet 1: what you'll do]
- [bullet 2: key approach/scope detail]
- [bullet 3: any notable constraint you're respecting]

Proceeding with [planning/implementation].
```

### If confidence < 95%

**Always report your current confidence level first.** This lets the user judge whether
the task needs minor clarification or a fundamental rethink before entering Q&A.

- **Below 50%**: The request is too vague for targeted questions to help efficiently.
  Tell the user the confidence is low and briefly explain what's missing at a high level.
  Suggest they restate the task with more detail rather than grinding through many rounds
  of Q&A. You may still ask 1-2 high-level framing questions if that would help.
- **50-94%**: Ask the minimum number of targeted questions needed to close the gap.
  Be specific — don't ask open-ended "can you tell me more?" questions.

Format:
```
**Task understanding** (confidence: X%)
- [what you DO understand]

[If < 50%]: This is quite open-ended — it would help if you could restate with more
detail on [the key missing pieces]. A couple of high-level questions:
1. [Framing question]

[If 50-94%]: Before I proceed, I need to clarify:
1. [Specific question about the ambiguous part]
2. [Another specific question if needed]
```

**Rules for clarifying questions:**
- Always show the confidence % so the user can gauge the gap
- Ask only what you actually need — don't pad with unnecessary questions
- Each question should target a specific ambiguity, not general context
- Offer your best-guess options when possible (e.g. "Should X do A or B?")
- Maximum 4 questions per round — if you have more, prioritise the most blocking ones
- After receiving answers, re-run the confidence check and report the new %;
  repeat until >= 95%

## Important boundaries

- This gate applies to **implementation tasks**, not informational questions
- Do NOT use this as an excuse to stall on simple, well-defined work
- Single-file edits with clear instructions, quick lookups, and conversational
  replies should skip this entirely
- If the user says "just do it" or signals urgency, state your assumptions
  briefly and proceed — don't block on ceremony
