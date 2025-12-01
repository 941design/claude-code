# Agent Authoring Guidelines

This guide captures common practices used across the existing agent definitions to help you craft new agents that are consistent, high-quality, and easy to consume.

## Frontmatter Essentials
- **Name and description**: Use a concise, kebab-case `name` and a descriptive `description` that explains when to trigger the agent. Include proactive trigger cues and brief scenarios where helpful.
- **Model and color**: Specify an appropriate `model` (inherit/haiku/sonnet/opus) and UI `color` to signal importance and persona.
- **Tools**: Declare the exact tools the agent relies on. Prefer explicit lists (e.g., `Read`, `Glob`, `Grep`, `Task`, `Bash`) that match the workflow you describe.
- **Examples**: Add `<example>` blocks that show realistic invocations and commentary explaining why the agent is appropriate for each case.

## Define a Clear Mission
- Open with a sentence that states the agent’s specialization (e.g., “expert code analyst,” “plugin validator,” “SDK verifier”).
- Clarify primary goals and boundaries up front. Call out what the agent should **not** focus on to prevent scope creep.

## Structure the Workflow
Organize guidance into explicit sections that mirror how the agent should operate:
- **Focus areas or responsibilities**: List core duties (e.g., “architecture analysis,” “SDK usage validation,” “error-handling review”).
- **Step-by-step process**: Provide a numbered flow the agent should follow (discovery → analysis → reporting). Include when to consult docs or external references.
- **Input scope defaults**: State what to review by default (e.g., unstaged git diff) and how users can override the scope.
- **Parallel/serial behavior**: If the agent launches other agents or tools, note when to do so in sequence vs. parallel and how to consolidate results.

### Example Workflow Skeleton
```markdown
You are an expert <specialty>.

## Core Responsibilities
- ...

## Process
1. Locate relevant files (use Glob/Grep)
2. Read key sources and trace flows
3. Validate against <rules or docs>
4. Summarize findings with file:line references

## Output Format
- Overall status (PASS/WARN/FAIL)
- Critical issues with file:line and fixes
- Warnings and recommendations
- Positive findings
```

## Emphasize Actionable Outputs
- Require file and line references for observations.
- Group issues by severity and filter out low-confidence findings. Include a confidence rubric when false positives are a concern.
- Provide concrete fix suggestions and highlight positives, not just problems.
- Use consistent headings (“Summary,” “Critical Issues,” “Warnings,” “Positive Findings,” “Recommendations”) to make reports skimmable.

## Tooling and Safety
- Instruct agents to consult authoritative sources (e.g., official docs) when validating specialized work.
- Specify security checks (no secrets, safe URLs) when relevant.
- Encourage idempotent reads first; avoid destructive actions unless absolutely required.

## Encourage Repeatability
- Default to reviewing current or recent changes (e.g., `git diff`) so results stay relevant.
- Remind users to rerun agents after fixes, and to state the scope if it differs from the default.
