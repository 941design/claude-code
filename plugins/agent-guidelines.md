# Agent Authoring Guidelines

This guide captures common practices used across the existing agent definitions to help you craft new agents that are consistent, high-quality, and easy to consume.

## Frontmatter Essentials
- **Name and description**: Use a concise, kebab-case `name` and a descriptive `description` that explains when to trigger the agent. Include proactive trigger cues and brief scenarios where helpful.

  [plugin-dev/agents/plugin-validator.md](plugin-dev/agents/plugin-validator.md)
  > name: plugin-validator
  > description: Use this agent when the user asks to "validate my plugin", "check plugin structure", "verify plugin is correct", "validate plugin.json", "check plugin files", or mentions plugin validation. Also trigger proactively after user creates or modifies plugin components. Examples:
  >
  > <example>
  > Context: User finished creating a new plugin
  > user: "I've created my first plugin with commands and hooks"
  > assistant: "Great! Let me validate the plugin structure."
- **Model and color**: Specify an appropriate `model` (inherit/haiku/sonnet/opus) and UI `color` to signal importance and persona.

  [feature-dev/agents/code-architect.md](feature-dev/agents/code-architect.md)
  > model: sonnet
  > color: green
- **Tools**: Declare the exact tools the agent relies on. Prefer explicit lists (e.g., `Read`, `Glob`, `Grep`, `Task`, `Bash`) that match the workflow you describe.

  [feature-dev/agents/code-architect.md](feature-dev/agents/code-architect.md)
  > tools: Glob, Grep, LS, Read, NotebookRead, WebFetch, TodoWrite, WebSearch, KillShell, BashOutput
- **Examples**: Add `<example>` blocks that show realistic invocations and commentary explaining why the agent is appropriate for each case.

  [plugin-dev/agents/plugin-validator.md](plugin-dev/agents/plugin-validator.md)
  > <example>
  > Context: User explicitly requests validation
  > user: "Validate my plugin before I publish it"
  > assistant: "I'll use the plugin-validator agent to perform comprehensive validation."
  > <commentary>
  > Explicit validation request triggers the agent.
  > </commentary>
  > </example>

## Define a Clear Mission
- Open with a sentence that states the agent’s specialization (e.g., “expert code analyst,” “plugin validator,” “SDK verifier”).

  [feature-dev/agents/code-architect.md](feature-dev/agents/code-architect.md)
  > You are a senior software architect who delivers comprehensive, actionable architecture blueprints by deeply understanding codebases and making confident architectural decisions.
- Clarify primary goals and boundaries up front. Call out what the agent should **not** focus on to prevent scope creep.

  [agent-sdk-dev/agents/agent-sdk-verifier-py.md#what-not-to-focus-on](agent-sdk-dev/agents/agent-sdk-verifier-py.md#what-not-to-focus-on)
  > ## What NOT to Focus On
  >
  > - General code style preferences (PEP 8 formatting, naming conventions, etc.)
  > - Python-specific style choices (snake_case vs camelCase debates)
  > - Import ordering preferences
  > - General Python best practices unrelated to SDK usage

## Structure the Workflow
Organize guidance into explicit sections that mirror how the agent should operate:
- **Focus areas or responsibilities**: List core duties (e.g., “architecture analysis,” “SDK usage validation,” “error-handling review”).

  [plugin-dev/agents/plugin-validator.md](plugin-dev/agents/plugin-validator.md)
  > **Your Core Responsibilities:**
  > 1. Validate plugin structure and organization
  > 2. Check plugin.json manifest for correctness
  > 3. Validate all component files (commands, agents, skills, hooks)
  > 4. Verify naming conventions and file organization
  > 5. Check for common issues and anti-patterns
  > 6. Provide specific, actionable recommendations
- **Step-by-step process**: Provide a numbered flow the agent should follow (discovery → analysis → reporting). Include when to consult docs or external references.

  [agent-sdk-dev/agents/agent-sdk-verifier-py.md#verification-process](agent-sdk-dev/agents/agent-sdk-verifier-py.md#verification-process)
  > 2. **Check SDK Documentation Adherence**:
  >
  >    - Use WebFetch to reference the official Python SDK docs: https://docs.claude.com/en/api/agent-sdk/python
  >    - Compare the implementation against official patterns and recommendations
  >    - Note any deviations from documented best practices
- **Input scope defaults**: State what to review by default (e.g., unstaged git diff) and how users can override the scope.

  [plugin-dev/agents/plugin-validator.md](plugin-dev/agents/plugin-validator.md)
  > 1. **Locate Plugin Root**:
  >    - Check for `.claude-plugin/plugin.json`
  >    - Verify plugin directory structure
  >    - Note plugin location (project vs marketplace)
- **Parallel/serial behavior**: If the agent launches other agents or tools, note when to do so in sequence vs. parallel and how to consolidate results.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > **Sequential approach** (one at a time):
  > - Easier to understand and act on
  > - Each report is complete before next
  > - Good for interactive review
  >
  > **Parallel approach** (user can request):
  > - Launch all agents simultaneously
  > - Faster for comprehensive review
  > - Results come back together
  >
  > After agents complete, summarize:
  > - **Critical Issues** (must fix before merge)
  > - **Important Issues** (should fix)
  > - **Suggestions** (nice to have)
  > - **Positive Observations** (what's good)

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

  [plugin-dev/agents/plugin-validator.md](plugin-dev/agents/plugin-validator.md)
  > **Quality Standards:**
  > - All validation errors include file path and specific issue
  > - Warnings distinguished from errors
- Group issues by severity and filter out low-confidence findings. Include a confidence rubric when false positives are a concern.

  [pr-review-toolkit/agents/code-reviewer.md#issue-confidence-scoring](pr-review-toolkit/agents/code-reviewer.md#issue-confidence-scoring)
  > **Issue Confidence Scoring**
  >
  > Rate each issue from 0-100:
  >
  > - **0-25**: Likely false positive or pre-existing issue
  > - **26-50**: Minor nitpick not explicitly in CLAUDE.md
  > - **51-75**: Valid but low-impact issue
  > - **76-90**: Important issue requiring attention
  > - **91-100**: Critical bug or explicit CLAUDE.md violation
  >
  > **Only report issues with confidence ≥ 80**
- Provide concrete fix suggestions and highlight positives, not just problems.

  [plugin-dev/agents/plugin-validator.md#positive-findings](plugin-dev/agents/plugin-validator.md#positive-findings)
  > ### Positive Findings
  > - [What's done well]
  >
  > ### Recommendations
  > 1. [Priority recommendation]
  > 2. [Additional recommendation]
- Use consistent headings (“Summary,” “Critical Issues,” “Warnings,” “Positive Findings,” “Recommendations”) to make reports skimmable.

  [feature-dev/agents/code-architect.md#output-guidance](feature-dev/agents/code-architect.md#output-guidance)
  > - **Patterns & Conventions Found**: Existing patterns with file:line references, similar features, key abstractions
  > - **Architecture Decision**: Your chosen approach with rationale and trade-offs
  > - **Component Design**: Each component with file path, responsibilities, dependencies, and interfaces
  > - **Implementation Map**: Specific files to create/modify with detailed change descriptions
  > - **Data Flow**: Complete flow from entry points through transformations to outputs
  > - **Build Sequence**: Phased implementation steps as a checklist

## Tooling and Safety
- Instruct agents to consult authoritative sources (e.g., official docs) when validating specialized work.

  [agent-sdk-dev/agents/agent-sdk-verifier-py.md#verification-process](agent-sdk-dev/agents/agent-sdk-verifier-py.md#verification-process)
  > - Use WebFetch to reference the official Python SDK docs: https://docs.claude.com/en/api/agent-sdk/python
  > - Compare the implementation against official patterns and recommendations
- Specify security checks (no secrets, safe URLs) when relevant.

  [plugin-dev/agents/plugin-validator.md](plugin-dev/agents/plugin-validator.md)
  > 10. **Security Checks**:
  >     - No hardcoded credentials in any files
  >     - MCP servers use HTTPS/WSS not HTTP/WS
  >     - Hooks don't have obvious security issues
- Encourage idempotent reads first; avoid destructive actions unless absolutely required.

  [hookify/agents/conversation-analyzer.md](hookify/agents/conversation-analyzer.md)
  > tools: ["Read", "Grep"]

## Encourage Repeatability
- Default to reviewing current or recent changes (e.g., `git diff`) so results stay relevant.

  [pr-review-toolkit/agents/code-reviewer.md#review-scope](pr-review-toolkit/agents/code-reviewer.md#review-scope)
  > By default, review unstaged changes from `git diff`. The user may specify different files or scope to review.
- Remind users to rerun agents after fixes, and to state the scope if it differs from the default.

  [pr-review-toolkit/commands/review-pr.md#review-workflow](pr-review-toolkit/commands/review-pr.md#review-workflow)
  > 4. Re-run review after fixes
