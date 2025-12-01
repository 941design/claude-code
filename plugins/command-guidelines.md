# Command Authoring Guidelines

Use these practices to craft clear, actionable command workflows consistent with the existing plugins.

## Frontmatter Checklist
- **description**: One-line purpose of the command.
- **argument-hint**: Brief cue for optional arguments or expected input format.
- **allowed-tools**: Explicit list of permitted tools (e.g., `Bash`, `Glob`, `Task`, `Read`). Include command-specific constraints like `Bash(git commit:*)` when needed.
- Optional fields such as `disable-model-invocation` should be set deliberately when tool-only behavior is required.

## Provide Context Up Front
- Start with sections like **Context** or **Goal** to anchor the workflow.
- Surface auto-fetched state (git status, diff, branch) so the user sees what the command will act on.
- Clarify the expected outcome and any non-negotiable constraints (e.g., “create a single git commit and do nothing else”).

## Structure the Workflow
Break the body into digestible phases with headings and checklists:
- **Discovery/Scope**: Identify files, feature scope, or PR eligibility before acting.
- **Planning**: Encourage clarifying questions and todo lists for underspecified tasks.
- **Execution Steps**: Numbered instructions that align with available tools; note when to run agents in parallel vs. sequentially.
- **Quality/Review**: Integrate specialized agents or follow-up checks (e.g., architecture review, test analysis) with confidence filtering.
- **Summary/Output**: Specify exact output format or comment template to ensure consistent results.

### Example Flow Skeleton
```markdown
# <Command Title>

## Phase 1: Discovery
- Capture current git status and diff
- Ask clarifying questions if requirements are unclear

## Phase 2: Execution
1. Launch supporting agents (if any)
2. Read files identified
3. Apply changes following project conventions

## Phase 3: Quality Review
- Run review agents focused on bugs, guidelines, and edge cases
- Filter out low-confidence findings

## Phase 4: Summary
- List what changed, files touched, next steps
```

## Usage Examples and Tips
- Provide concrete invocation examples (full run, scoped aspects, parallel mode) to guide users.
- Include tips for when to run the command (early in development, before PRs, after feedback).
- Offer templates for final comments or reports with required links, headings, and citation expectations.

## Tooling Discipline
- Map each instruction to allowed tools—avoid suggesting actions the command cannot perform.
- When restricting behavior, state it explicitly (e.g., “stage and commit in one message; do not send other text”).
- Encourage safe defaults: read before writing, avoid unnecessary builds, respect repository conventions.

## Encourage Iteration
- Advise rerunning commands after fixes or new context.
- Recommend addressing critical issues first, then important ones, and noting positives to keep feedback balanced.
