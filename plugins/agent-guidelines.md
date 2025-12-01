# Agent Authoring Guidelines

This guide captures common practices used across the existing agent definitions to help you craft new agents that are consistent, high-quality, and easy to consume.

## Frontmatter Essentials
- **Name and description**: Use a concise, kebab-case `name` and a descriptive `description` that explains when to trigger the agent. Include proactive trigger cues and brief scenarios where helpful.
  > name: plugin-validator
  > description: Use this agent when the user asks to "validate my plugin", "check plugin structure", "verify plugin is correct", "validate plugin.json", "check plugin files", or mentions plugin validation. Also trigger proactively after user creates or modifies plugin components. Examples:
  >
  > <example>
  > Context: User finished creating a new plugin
  > user: "I've created my first plugin with commands and hooks"
  > assistant: "Great! Let me validate the plugin structure." ([plugin-dev/agents/plugin-validator.md#L2-L16](plugin-dev/agents/plugin-validator.md#L2-L16))
- **Model and color**: Specify an appropriate `model` (inherit/haiku/sonnet/opus) and UI `color` to signal importance and persona.
  > model: sonnet
  > color: green ([feature-dev/agents/code-architect.md#L5-L6](feature-dev/agents/code-architect.md#L5-L6))
- **Tools**: Declare the exact tools the agent relies on. Prefer explicit lists (e.g., `Read`, `Glob`, `Grep`, `Task`, `Bash`) that match the workflow you describe.
  > tools: Glob, Grep, LS, Read, NotebookRead, WebFetch, TodoWrite, WebSearch, KillShell, BashOutput ([feature-dev/agents/code-architect.md#L4-L4](feature-dev/agents/code-architect.md#L4-L4))
- **Examples**: Add `<example>` blocks that show realistic invocations and commentary explaining why the agent is appropriate for each case.
  > <example>
  > Context: User explicitly requests validation
  > user: "Validate my plugin before I publish it"
  > assistant: "I'll use the plugin-validator agent to perform comprehensive validation."
  > <commentary>
  > Explicit validation request triggers the agent.
  > </commentary>
  > </example> ([plugin-dev/agents/plugin-validator.md#L17-L26](plugin-dev/agents/plugin-validator.md#L17-L26))

## Define a Clear Mission
- Open with a sentence that states the agent’s specialization (e.g., “expert code analyst,” “plugin validator,” “SDK verifier”).
  > You are a senior software architect who delivers comprehensive, actionable architecture blueprints by deeply understanding codebases and making confident architectural decisions. ([feature-dev/agents/code-architect.md#L9-L10](feature-dev/agents/code-architect.md#L9-L10))
- Clarify primary goals and boundaries up front. Call out what the agent should **not** focus on to prevent scope creep.
  > ## What NOT to Focus On
  >
  > - General code style preferences (PEP 8 formatting, naming conventions, etc.)
  > - Python-specific style choices (snake_case vs camelCase debates)
  > - Import ordering preferences
  > - General Python best practices unrelated to SDK usage ([agent-sdk-dev/agents/agent-sdk-verifier-py.md#L71-L79](agent-sdk-dev/agents/agent-sdk-verifier-py.md#L71-L79))

## Structure the Workflow
Organize guidance into explicit sections that mirror how the agent should operate:
- **Focus areas or responsibilities**: List core duties (e.g., “architecture analysis,” “SDK usage validation,” “error-handling review”).
  > **Your Core Responsibilities:**
  > 1. Validate plugin structure and organization
  > 2. Check plugin.json manifest for correctness
  > 3. Validate all component files (commands, agents, skills, hooks)
  > 4. Verify naming conventions and file organization
  > 5. Check for common issues and anti-patterns
  > 6. Provide specific, actionable recommendations ([plugin-dev/agents/plugin-validator.md#L36-L43](plugin-dev/agents/plugin-validator.md#L36-L43))
- **Step-by-step process**: Provide a numbered flow the agent should follow (discovery → analysis → reporting). Include when to consult docs or external references.
  > 2. **Check SDK Documentation Adherence**:
  >
  >    - Use WebFetch to reference the official Python SDK docs: https://docs.claude.com/en/api/agent-sdk/python
  >    - Compare the implementation against official patterns and recommendations
  >    - Note any deviations from documented best practices ([agent-sdk-dev/agents/agent-sdk-verifier-py.md#L86-L92](agent-sdk-dev/agents/agent-sdk-verifier-py.md#L86-L92))
- **Input scope defaults**: State what to review by default (e.g., unstaged git diff) and how users can override the scope.
  > 1. **Locate Plugin Root**:
  >    - Check for `.claude-plugin/plugin.json`
  >    - Verify plugin directory structure
  >    - Note plugin location (project vs marketplace) ([plugin-dev/agents/plugin-validator.md#L49-L54](plugin-dev/agents/plugin-validator.md#L49-L54))
- **Parallel/serial behavior**: If the agent launches other agents or tools, note when to do so in sequence vs. parallel and how to consolidate results.
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
  > - **Positive Observations** (what's good) ([pr-review-toolkit/commands/review-pr.md#L40-L64](pr-review-toolkit/commands/review-pr.md#L40-L64))

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
  > **Quality Standards:**
  > - All validation errors include file path and specific issue
  > - Warnings distinguished from errors ([plugin-dev/agents/plugin-validator.md#L135-L139](plugin-dev/agents/plugin-validator.md#L135-L139))
- Group issues by severity and filter out low-confidence findings. Include a confidence rubric when false positives are a concern.
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
  > **Only report issues with confidence ≥ 80** ([pr-review-toolkit/agents/code-reviewer.md#L33-L47](pr-review-toolkit/agents/code-reviewer.md#L33-L47))
- Provide concrete fix suggestions and highlight positives, not just problems.
  > ### Positive Findings
  > - [What's done well]
  >
  > ### Recommendations
  > 1. [Priority recommendation]
  > 2. [Additional recommendation] ([plugin-dev/agents/plugin-validator.md#L159-L171](plugin-dev/agents/plugin-validator.md#L159-L171))
- Use consistent headings (“Summary,” “Critical Issues,” “Warnings,” “Positive Findings,” “Recommendations”) to make reports skimmable.
  > - **Patterns & Conventions Found**: Existing patterns with file:line references, similar features, key abstractions
  > - **Architecture Decision**: Your chosen approach with rationale and trade-offs
  > - **Component Design**: Each component with file path, responsibilities, dependencies, and interfaces
  > - **Implementation Map**: Specific files to create/modify with detailed change descriptions
  > - **Data Flow**: Complete flow from entry points through transformations to outputs
  > - **Build Sequence**: Phased implementation steps as a checklist ([feature-dev/agents/code-architect.md#L24-L31](feature-dev/agents/code-architect.md#L24-L31))

## Tooling and Safety
- Instruct agents to consult authoritative sources (e.g., official docs) when validating specialized work.
  > - Use WebFetch to reference the official Python SDK docs: https://docs.claude.com/en/api/agent-sdk/python
  > - Compare the implementation against official patterns and recommendations ([agent-sdk-dev/agents/agent-sdk-verifier-py.md#L86-L92](agent-sdk-dev/agents/agent-sdk-verifier-py.md#L86-L92))
- Specify security checks (no secrets, safe URLs) when relevant.
  > 10. **Security Checks**:
  >     - No hardcoded credentials in any files
  >     - MCP servers use HTTPS/WSS not HTTP/WS
  >     - Hooks don't have obvious security issues ([plugin-dev/agents/plugin-validator.md#L126-L134](plugin-dev/agents/plugin-validator.md#L126-L134))
- Encourage idempotent reads first; avoid destructive actions unless absolutely required.
  > tools: ["Read", "Grep"] ([hookify/agents/conversation-analyzer.md#L6-L6](hookify/agents/conversation-analyzer.md#L6-L6))

## Encourage Repeatability
- Default to reviewing current or recent changes (e.g., `git diff`) so results stay relevant.
  > By default, review unstaged changes from `git diff`. The user may specify different files or scope to review. ([pr-review-toolkit/agents/code-reviewer.md#L17-L32](pr-review-toolkit/agents/code-reviewer.md#L17-L32))
- Remind users to rerun agents after fixes, and to state the scope if it differs from the default.
  > 4. Re-run review after fixes ([pr-review-toolkit/commands/review-pr.md#L87-L88](pr-review-toolkit/commands/review-pr.md#L87-L88))
