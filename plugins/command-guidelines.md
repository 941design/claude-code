# Command Authoring Guidelines

Use these practices to craft clear, actionable command workflows consistent with the existing plugins.

## Frontmatter Checklist
- **description**: One-line purpose of the command. Example: `feature-dev` states “Guided feature development with codebase understanding and architecture focus” in its frontmatter.【F:plugins/feature-dev/commands/feature-dev.md†L1-L3】
- **argument-hint**: Brief cue for optional arguments or expected input format. Example: `feature-dev` adds `argument-hint: Optional feature description` to signal optional input.【F:plugins/feature-dev/commands/feature-dev.md†L1-L4】
- **allowed-tools**: Explicit list of permitted tools (e.g., `Bash`, `Glob`, `Task`, `Read`). Include command-specific constraints like `Bash(git commit:*)` when needed. Example: `commit-push-pr` restricts Bash usage to specific git/gh subcommands for a tight workflow.【F:plugins/commit-commands/commands/commit-push-pr.md†L1-L3】
- Optional fields such as `disable-model-invocation` should be set deliberately when tool-only behavior is required. Example: `code-review` sets `disable-model-invocation: false` to explicitly allow agent calls while remaining tool-centric.【F:plugins/code-review/commands/code-review.md†L1-L5】

## Provide Context Up Front
- Start with sections like **Context** or **Goal** to anchor the workflow. Example: `commit-push-pr` opens with a “Context” section listing repo state before instructions.【F:plugins/commit-commands/commands/commit-push-pr.md†L6-L11】
- Surface auto-fetched state (git status, diff, branch) so the user sees what the command will act on. Example: the same command inlines `git status`, `git diff`, and branch name for immediate visibility.【F:plugins/commit-commands/commands/commit-push-pr.md†L6-L11】
- Clarify the expected outcome and any non-negotiable constraints (e.g., “create a single git commit and do nothing else”). Example: `commit-push-pr` mandates performing commit, push, and PR creation in a single message without extra text.【F:plugins/commit-commands/commands/commit-push-pr.md†L16-L20】

## Structure the Workflow
Break the body into digestible phases with headings and checklists:
- **Discovery/Scope**: Identify files, feature scope, or PR eligibility before acting. Example: `review-pr` starts with determining review scope and applicable aspects before launching analyses.【F:plugins/pr-review-toolkit/commands/review-pr.md†L15-L29】
- **Planning**: Encourage clarifying questions and todo lists for underspecified tasks. Example: `feature-dev` Phase 1 asks targeted questions to clarify the feature before proceeding.【F:plugins/feature-dev/commands/feature-dev.md†L20-L33】
- **Execution Steps**: Numbered instructions that align with available tools; note when to run agents in parallel vs. sequentially. Example: `review-pr` enumerates launch steps and offers sequential vs. parallel approaches for agent runs.【F:plugins/pr-review-toolkit/commands/review-pr.md†L30-L57】
- **Quality/Review**: Integrate specialized agents or follow-up checks (e.g., architecture review, test analysis) with confidence filtering. Example: `feature-dev` Phase 6 orchestrates multiple code-reviewer agents and consolidates findings by severity.【F:plugins/feature-dev/commands/feature-dev.md†L101-L110】
- **Summary/Output**: Specify exact output format or comment template to ensure consistent results. Example: `review-pr` provides a templated “PR Review Summary” markdown block for aggregating issues.【F:plugins/pr-review-toolkit/commands/review-pr.md†L65-L88】

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
- Provide concrete invocation examples (full run, scoped aspects, parallel mode) to guide users. Example: `review-pr` lists default, aspect-specific, and parallel invocation examples in its usage section.【F:plugins/pr-review-toolkit/commands/review-pr.md†L90-L113】
- Include tips for when to run the command (early in development, before PRs, after feedback). Example: `review-pr` includes “Tips” and “Workflow Integration” timing guidance (before committing, before PR, after feedback).【F:plugins/pr-review-toolkit/commands/review-pr.md†L148-L181】
- Offer templates for final comments or reports with required links, headings, and citation expectations. Example: `code-review` supplies an explicit PR comment format including sha-linked references.【F:plugins/code-review/commands/code-review.md†L50-L92】

## Tooling Discipline
- Map each instruction to allowed tools—avoid suggesting actions the command cannot perform. Example: `review-pr` aligns its steps with `Bash`, `Read`, and agent launches listed in `allowed-tools`.【F:plugins/pr-review-toolkit/commands/review-pr.md†L4-L44】
- When restricting behavior, state it explicitly (e.g., “stage and commit in one message; do not send other text”). Example: `commit-push-pr` forbids extra output and requires all tool calls in a single message.【F:plugins/commit-commands/commands/commit-push-pr.md†L16-L20】
- Encourage safe defaults: read before writing, avoid unnecessary builds, respect repository conventions. Example: `code-review` forbids running builds and leans on `gh` for read-only interactions unless posting the final comment.【F:plugins/code-review/commands/code-review.md†L44-L48】

## Encourage Iteration
- Advise rerunning commands after fixes or new context. Example: `review-pr` tells users to re-run reviews after addressing critical items.【F:plugins/pr-review-toolkit/commands/review-pr.md†L83-L88】
- Recommend addressing critical issues first, then important ones, and noting positives to keep feedback balanced. Example: `review-pr` orders recommended actions from critical fixes through suggestions and encourages calling out strengths.【F:plugins/pr-review-toolkit/commands/review-pr.md†L65-L87】
