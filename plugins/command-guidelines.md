# Command Authoring Guidelines

Use these practices to craft clear, actionable command workflows consistent with the existing plugins.

## Frontmatter Checklist
- **description**: One-line purpose of the command.

  [feature-dev/commands/feature-dev.md](feature-dev/commands/feature-dev.md)
  > description: Guided feature development with codebase understanding and architecture focus
- **argument-hint**: Brief cue for optional arguments or expected input format.

  [feature-dev/commands/feature-dev.md](feature-dev/commands/feature-dev.md)
  > argument-hint: Optional feature description
- **allowed-tools**: Explicit list of permitted tools (e.g., `Bash`, `Glob`, `Task`, `Read`). Include command-specific constraints like `Bash(git commit:*)` when needed.

  [commit-commands/commands/commit-push-pr.md](commit-commands/commands/commit-push-pr.md)
  > allowed-tools: Bash(git checkout --branch:*), Bash(git add:*), Bash(git status:*), Bash(git push:*), Bash(git commit:*), Bash(gh pr create:*)
- Optional fields such as `disable-model-invocation` should be set deliberately when tool-only behavior is required.

  [code-review/commands/code-review.md](code-review/commands/code-review.md)
  > disable-model-invocation: false

## Provide Context Up Front
- Start with sections like **Context** or **Goal** to anchor the workflow.

  [commit-commands/commands/commit-push-pr.md#context](commit-commands/commands/commit-push-pr.md#context)
  > ## Context
  >
  > - Current git status: !`git status`
  > - Current git diff (staged and unstaged changes): !`git diff HEAD`
  > - Current branch: !`git branch --show-current`
- Surface auto-fetched state (git status, diff, branch) so the user sees what the command will act on.

  [commit-commands/commands/commit-push-pr.md#context](commit-commands/commands/commit-push-pr.md#context)
  > - Current git status: !`git status`
  > - Current git diff (staged and unstaged changes): !`git diff HEAD`
  > - Current branch: !`git branch --show-current`
- Clarify the expected outcome and any non-negotiable constraints (e.g., “create a single git commit and do nothing else”).

  [commit-commands/commands/commit-push-pr.md#your-task](commit-commands/commands/commit-push-pr.md#your-task)
  > 5. You have the capability to call multiple tools in a single response. You MUST do all of the above in a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.

## Structure the Workflow
Break the body into digestible phases with headings and checklists:
- **Discovery/Scope**: Identify files, feature scope, or PR eligibility before acting.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > 1. **Determine Review Scope**
  >    - Read the PR diff: !`gh pr diff`
  >    - Identify files changed and their types
- **Planning**: Encourage clarifying questions and todo lists for underspecified tasks.

  [feature-dev/commands/feature-dev.md#phase-1-discovery](feature-dev/commands/feature-dev.md#phase-1-discovery)
  > **Actions**:
  > 1. Create todo list with all phases
  > 2. If feature unclear, ask user for:
  >    - What problem are they solving?
  >    - What should the feature do?
  >    - Any constraints or requirements?
  > 3. Summarize understanding and confirm with user
- **Execution Steps**: Numbered instructions that align with available tools; note when to run agents in parallel vs. sequentially.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > 5. **Launch Review Agents**
  >
  >    **Sequential approach** (one at a time):
  >    - Easier to understand and act on
  >    - Each report is complete before next
  >    - Good for interactive review
  >
  >    **Parallel approach** (user can request):
  >    - Launch all agents simultaneously
  >    - Faster for comprehensive review
  >    - Results come back together
- **Quality/Review**: Integrate specialized agents or follow-up checks (e.g., architecture review, test analysis) with confidence filtering.

  [feature-dev/commands/feature-dev.md#phase-6-quality-review](feature-dev/commands/feature-dev.md#phase-6-quality-review)
  > 1. Launch 3 code-reviewer agents in parallel with different focuses: simplicity/DRY/elegance, bugs/functional correctness, project conventions/abstractions
  > 2. Consolidate findings and identify highest severity issues that you recommend fixing
  > 3. **Present findings to user and ask what they want to do** (fix now, fix later, or proceed as-is)
- **Summary/Output**: Specify exact output format or comment template to ensure consistent results.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > # PR Review Summary
  >
  > ## Critical Issues
  > - ...
  >
  > ## Important Issues
  > - ...
  >
  > ## Suggestions
  > - ...
  >
  > ## Strengths
  > - What's well-done in this PR
  >
  > ## Recommended Action
  > 1. Fix critical issues first
  > 2. Address important issues
  > 3. Consider suggestions
  > 4. Re-run review after fixes

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

  [pr-review-toolkit/commands/review-pr.md#usage-examples](pr-review-toolkit/commands/review-pr.md#usage-examples)
  > /pr-review-toolkit:review-pr
  >
  > /pr-review-toolkit:review-pr "tests"
  >
  > /pr-review-toolkit:review-pr "parallel"
- Include tips for when to run the command (early in development, before PRs, after feedback).

  [pr-review-toolkit/commands/review-pr.md#tips](pr-review-toolkit/commands/review-pr.md#tips)
  > **Before committing**: Catch issues early in your workflow
  > **Before PR**: Validate changes before requesting review
  > **After feedback**: Verify fixes before re-requesting review
- Offer templates for final comments or reports with required links, headings, and citation expectations.

  [code-review/commands/code-review.md#code-review-1](code-review/commands/code-review.md#code-review-1)
  > ```markdown
  > [Status] Review of {pr-url}
  >
  > Summary: [overall impression + risk assessment]
  >
  > Issues:
  > - [markdown link to file/line: description]
  > - ...
  >
  > Good Stuff:
  > - [positive note]
  > - ...
  > ```

## Tooling Discipline
- Map each instruction to allowed tools—avoid suggesting actions the command cannot perform.

  [pr-review-toolkit/commands/review-pr.md#comprehensive-pr-review](pr-review-toolkit/commands/review-pr.md#comprehensive-pr-review)
  > allowed-tools: ["Bash", "Glob", "Grep", "Read", "Task"]
- When restricting behavior, state it explicitly (e.g., “stage and commit in one message; do not send other text”).

  [commit-commands/commands/commit-push-pr.md#your-task](commit-commands/commands/commit-push-pr.md#your-task)
  > You must do all 5 commands at once, in a single message, using *only* the five tool calls above. Do not send a separate confirmation message after the tool calls.
- Encourage safe defaults: read before writing, avoid unnecessary builds, respect repository conventions.

  [code-review/commands/code-review.md](code-review/commands/code-review.md)
  > You should not run the build.

## Encourage Iteration
- Advise rerunning commands after fixes or new context.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > 4. Re-run review after fixes
- Recommend addressing critical issues first, then important ones, and noting positives to keep feedback balanced.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > ## Recommended Action
  > 1. Fix critical issues first
  > 2. Address important issues
  > 3. Consider suggestions
  > 4. Re-run review after fixes
