# Agent Authoring Guidelines

This guide captures common practices used across the existing agent definitions to help you craft new agents that are consistent, high-quality, and easy to consume.

## Frontmatter Essentials
- **Name and description**: Use a concise, kebab-case `name` and a descriptive `description` that explains when to trigger the agent. Include proactive trigger cues and brief scenarios where helpful.
  > Example: The `plugin-validator` frontmatter pairs a precise name with triggers like “validate my plugin” to guide invocation. ([plugins/plugin-dev/agents/plugin-validator.md#L2-L32](plugins/plugin-dev/agents/plugin-validator.md#L2-L32))
- **Model and color**: Specify an appropriate `model` (inherit/haiku/sonnet/opus) and UI `color` to signal importance and persona.
  > Example: `code-architect` sets `model: sonnet` and `color: green` to convey depth and emphasis. ([plugins/feature-dev/agents/code-architect.md#L4-L6](plugins/feature-dev/agents/code-architect.md#L4-L6))
- **Tools**: Declare the exact tools the agent relies on. Prefer explicit lists (e.g., `Read`, `Glob`, `Grep`, `Task`, `Bash`) that match the workflow you describe.
  > Example: `code-architect` lists discovery-heavy tools (`Glob`, `Grep`, `Read`, etc.) that align with its analysis mandate. ([plugins/feature-dev/agents/code-architect.md#L4-L4](plugins/feature-dev/agents/code-architect.md#L4-L4))
- **Examples**: Add `<example>` blocks that show realistic invocations and commentary explaining why the agent is appropriate for each case.
  > Example: `plugin-validator` includes multiple contextual `<example>` blocks that spell out when to run the agent. ([plugins/plugin-dev/agents/plugin-validator.md#L5-L32](plugins/plugin-dev/agents/plugin-validator.md#L5-L32))

## Define a Clear Mission
- Open with a sentence that states the agent’s specialization (e.g., “expert code analyst,” “plugin validator,” “SDK verifier”).
  > Example: `code-architect` opens with “You are a senior software architect…” to anchor its role. ([plugins/feature-dev/agents/code-architect.md#L9-L10](plugins/feature-dev/agents/code-architect.md#L9-L10))
- Clarify primary goals and boundaries up front. Call out what the agent should **not** focus on to prevent scope creep.
  > Example: `agent-sdk-verifier-py` dedicates a “What NOT to Focus On” section to exclude style nitpicks from its remit. ([plugins/agent-sdk-dev/agents/agent-sdk-verifier-py.md#L73-L79](plugins/agent-sdk-dev/agents/agent-sdk-verifier-py.md#L73-L79))

## Structure the Workflow
Organize guidance into explicit sections that mirror how the agent should operate:
- **Focus areas or responsibilities**: List core duties (e.g., “architecture analysis,” “SDK usage validation,” “error-handling review”).
  > Example: `plugin-validator` enumerates responsibilities like manifest checks and security validation up front. ([plugins/plugin-dev/agents/plugin-validator.md#L41-L47](plugins/plugin-dev/agents/plugin-validator.md#L41-L47))
- **Step-by-step process**: Provide a numbered flow the agent should follow (discovery → analysis → reporting). Include when to consult docs or external references.
  > Example: `agent-sdk-verifier-py` walks through verification steps, including referencing SDK docs via WebFetch. ([plugins/agent-sdk-dev/agents/agent-sdk-verifier-py.md#L80-L105](plugins/agent-sdk-dev/agents/agent-sdk-verifier-py.md#L80-L105))
- **Input scope defaults**: State what to review by default (e.g., unstaged git diff) and how users can override the scope.
  > Example: `plugin-validator` defaults to locating `.claude-plugin/plugin.json` and scanning discovered directories before deeper checks. ([plugins/plugin-dev/agents/plugin-validator.md#L51-L75](plugins/plugin-dev/agents/plugin-validator.md#L51-L75))
- **Parallel/serial behavior**: If the agent launches other agents or tools, note when to do so in sequence vs. parallel and how to consolidate results.
  > Example: The PR code reviewer coordinates sequential eligibility checks and parallel review agents before aggregation. ([plugins/pr-review-toolkit/agents/code-reviewer.md#L16-L38](plugins/pr-review-toolkit/agents/code-reviewer.md#L16-L38))

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
  > Example: `plugin-validator`’s quality standards insist on file-scoped errors with specific paths. ([plugins/plugin-dev/agents/plugin-validator.md#L136-L139](plugins/plugin-dev/agents/plugin-validator.md#L136-L139))
- Group issues by severity and filter out low-confidence findings. Include a confidence rubric when false positives are a concern.
  > Example: The PR `code-reviewer` agents score findings and drop issues below a confidence threshold. ([plugins/code-review/commands/code-review.md#L20-L27](plugins/code-review/commands/code-review.md#L20-L27))
- Provide concrete fix suggestions and highlight positives, not just problems.
  > Example: `plugin-validator` pairs recommendations with fix suggestions and positive findings sections. ([plugins/plugin-dev/agents/plugin-validator.md#L137-L169](plugins/plugin-dev/agents/plugin-validator.md#L137-L169))
- Use consistent headings (“Summary,” “Critical Issues,” “Warnings,” “Positive Findings,” “Recommendations”) to make reports skimmable.
  > Example: `code-architect`’s output guidance breaks deliverables into labeled sections like “Patterns & Conventions Found” and “Build Sequence.” ([plugins/feature-dev/agents/code-architect.md#L24-L32](plugins/feature-dev/agents/code-architect.md#L24-L32))

## Tooling and Safety
- Instruct agents to consult authoritative sources (e.g., official docs) when validating specialized work.
  > Example: `agent-sdk-verifier-py` explicitly references official SDK documentation for comparisons. ([plugins/agent-sdk-dev/agents/agent-sdk-verifier-py.md#L89-L93](plugins/agent-sdk-dev/agents/agent-sdk-verifier-py.md#L89-L93))
- Specify security checks (no secrets, safe URLs) when relevant.
  > Example: `plugin-validator`’s security checks flag hardcoded credentials and enforce HTTPS/WSS MCP URLs. ([plugins/plugin-dev/agents/plugin-validator.md#L130-L134](plugins/plugin-dev/agents/plugin-validator.md#L130-L134))
- Encourage idempotent reads first; avoid destructive actions unless absolutely required.
  > Example: `conversation-analyzer` relies solely on `Read`/`Grep` for analysis without making modifications. ([plugins/hookify/agents/conversation-analyzer.md#L2-L7](plugins/hookify/agents/conversation-analyzer.md#L2-L7))

## Encourage Repeatability
- Default to reviewing current or recent changes (e.g., `git diff`) so results stay relevant.
  > Example: PR review agents assume git diff context when selecting files to analyze. ([plugins/pr-review-toolkit/agents/code-reviewer.md#L26-L32](plugins/pr-review-toolkit/agents/code-reviewer.md#L26-L32))
- Remind users to rerun agents after fixes, and to state the scope if it differs from the default.
  > Example: The PR review workflow advises re-running reviews after addressing issues. ([plugins/pr-review-toolkit/commands/review-pr.md#L83-L88](plugins/pr-review-toolkit/commands/review-pr.md#L83-L88))
