---
name: eval-exec
description: "An evaluate-then-execute workflow for change-oriented tasks. Use when eval-exec is invoked through an agent tool before work that may modify files, configuration, data, deployments, or external state. Start with a read-only assessment and wait for explicit `execute` authorization."
---

# Eval Exec

Use this skill as a two-phase gate for work that may change state:

1. Assess the request using repository or system evidence.
2. Implement only after the user explicitly authorizes execution.

The gate is a collaboration protocol, not a security boundary. Continue to
follow the host agent's safety, approval, and tool-use rules.

## Activation

- Treat an invocation of `eval-exec` through the agent tool as starting the
  assessment phase for the current request.
- Do not require the user to restate the skill name or append `evaluate` or
  `评估` after the agent tool invokes the skill.
- Do not treat skill invocation as execution authorization.
- This skill governs behavior after the agent tool invokes it; whether the host
  exposes or selects the skill is outside this skill's guarantee.
- Recognize the literal Chinese command `执行` as the equivalent of
  `execute`.

During the assessment phase, allow only read-only inspection and reasoning.
Do not edit files, run state-changing commands, alter data or configuration,
deploy, send external messages, or perform other mutations.

## Assessment phase

Produce an implementation-backed assessment before making changes. Lead with
the decision, then include only the sections that help the user approve or
adjust the scope:

```text
Conclusion: [feasible / feasible with changes / not recommended / blocked]

Confirmed state and evidence:
- [specific file, route, configuration, log, measurement, or other evidence]

Recommendations (prioritized):
1. [highest-value change]
2. [next change]

Boundaries and risks:
- [compatibility, security, data, performance, or rollout concern]

Expected implementation scope:
- [components, files, APIs, tests, or systems likely involved]

Validation:
- [tests, checks, measurements, or acceptance criteria]

Expected result:
- [observable outcome]

If useful, continue to add details, align the scope, or adjust the plan.

Assessment status: Assessment only; no changes made. Waiting for `execute`.
```

For Chinese conversations, keep the same structure and recognize the literal
tokens `评估` and `执行`; do not require the user to translate the workflow.
Do not add a separate language-selection rule here: the host agent should use
the conversation context to choose the response language.

### Evidence and reasoning rules

- Inspect the actual repository, runtime, configuration, logs, or data path
  before making implementation claims.
- Distinguish observed facts, strong inferences, assumptions, and open
  questions.
- Identify the real control point or bottleneck instead of recommending a
  surface-level workaround.
- Explain why the recommended order matters and what a partial fix would not
  solve.
- Prefer the smallest change that satisfies the request and reuses existing
  interfaces, components, and data paths.
- State when the assessment is incomplete because evidence is unavailable.
- Do not report edits, tests, or measurements that were not actually made.

### Assessment modes

Choose the mode that best matches the request.

#### Performance or root-cause assessment

Report the measured or observed slow path, relevant timing or frequency,
call/lookup behavior, repeated work, and the highest-confidence bottleneck.
Separate the first-load path from cached or subsequent paths.

#### Feature feasibility assessment

Map the request to existing capabilities, identify what can be reused, list
the missing APIs or interactions, define important semantics, and call out
security or input-validation boundaries.

#### UI or interaction assessment

Describe the recommended information hierarchy, interaction states,
loading/error behavior, data source, navigation behavior, and the smallest
set of components and tests involved.

## Execution phase

Enter the execution phase only when all of the following are true:

- An assessment for the current request has been produced.
- The user sends the standalone command `execute` or `执行`.
- The user has not materially changed the scope since the assessment.

Before changing anything, briefly restate the approved scope. Then:

- Implement only the approved scope.
- Preserve unrelated user changes and existing behavior outside that scope.
- Use the repository's existing patterns and source of truth.
- Run proportionate validation and inspect the resulting diff.
- Report what changed, what was validated, and any remaining risk.

If the user changes the scope, asks a new change-oriented question, or adds a
material requirement, return to the assessment phase before implementing it.

Do not treat any of these as execution authorization by themselves:
`go ahead`, `continue`, `proceed`, `sounds good`, `do it`, `let's do it`,
`好的`, `继续`, or `可以`. Ask for the canonical `execute` / `执行` command
when an explicit gate is required.

## Safety boundaries

- Keep the assessment read-only, including when the request sounds urgent.
- Do not infer authorization for deployment, destructive operations, external
  communication, or unrelated cleanup from `execute`.
- Preserve the user's existing worktree changes.
- If an approved implementation requires a new permission, credential, or
  materially broader action, stop and ask for direction.
- Treat the explicit command as authorization to implement the latest
  unchanged assessment, not as permission to expand it.

## Examples

### English examples

**Assessment only**

Agent tool request: invoke `eval-exec` for "Fix the login timeout."

Action: inspect the relevant code and runtime evidence, then return an
assessment without editing anything.

**Execute the approved assessment**

User: `execute`

Action: implement the latest unchanged assessment, validate it, and report the
result.

**Scope changed**

User: `execute, and also redesign the settings page.`

Action: assess the new settings-page scope before making changes.

### 中文示例

**仅进行评估**

工具调用：`eval-exec`，请求为“修复登录超时”。

操作：检查相关代码和运行时证据，然后返回评估结果，不修改任何内容。

**执行已批准的评估**

用户：`执行`

操作：实施最新且未改变的评估范围，完成验证并报告结果。

**范围发生变化**

用户：`执行，并且还要重做设置页面。`

操作：先针对新的设置页面范围进行评估，再开始修改。
