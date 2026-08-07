# eval-exec

Evaluate-Execute

> Assess first. Execute only after explicit approval.

**English** | [简体中文](README.zh-CN.md)

`eval-exec` is a small workflow skill for coding agents, invoked through an
agent tool. It separates evidence-backed assessment from implementation so users can review
the proposed scope before the agent changes files, configuration, data, or
external state.

## Why use it?

Change requests often hide important questions: where is the real bottleneck,
which existing paths can be reused, what semantics or risks matter, and how
will success be verified? `eval-exec` makes those questions explicit without
turning every conversation into a planning ceremony.

A model can be highly capable at generating code without accurately aligning
with the user's requirements. It may produce an implementation that is
complete, internally consistent, and even test-passing while solving the
wrong problem or making technical trade-offs the user did not approve.

In other words, model capability determines how far a solution can be carried
out, but does not automatically determine whether that solution matches the
user's actual goal. Terms such as "high performance," "secure," and
"compatible" still need to be aligned against the concrete context, boundaries,
and acceptance criteria.

`eval-exec` catches this kind of direction drift before code changes begin. It
first asks the model to make its understanding of the requirements, impact
surface, and implementation approach explicit, then leaves the decision to
execute with the user.

The assessment focuses on three questions:

- **What does the goal mean?** Turn vague goals into observable behavior,
  boundaries, and acceptance criteria.
- **What will be affected?** Identify the files, interfaces, callers, data
  paths, and potential chain reactions involved.
- **What is the decision boundary?** Make clear which implementation details
  the agent may choose and which architectural decisions, trade-offs, and
  risks require user approval.

Assessment is therefore not about proving whether the model can write code;
it is about confirming what the model is preparing to write. When a direction
drift is exposed at the text layer, the scope or interpretation can usually be
adjusted at low cost. Once the drift has reached code, tests, and interface
contracts, correction becomes substantially more expensive.

Continued use of assessment also helps users track the project's structure and
the impact of accumulated changes, preserving overall direction while taking
advantage of the model's execution speed.

## How It Differs from Full Planning Workflows

`eval-exec` is not a full Plan Mode, architecture-design, or SDD process. It
is a lightweight pre-execution alignment step focused on three questions:

- Does the model correctly understand the current request?
- What implementation direction is the model preparing to take?
- What are the main impacts and risks of that direction?

The assessment phase may perform necessary read-only inspection, but it does
not modify files, configuration, data, or external state. The agent implements
the latest unchanged assessment only after the user sends the standalone
`execute` or `执行` command.

It is suited to everyday local changes, ambiguous requests, and tasks where
the direction should be confirmed before implementation. Architecture
rewrites, complex migrations, and collaborative projects may still require a
more complete planning and design process.

To keep an assessment lightweight, limit the requested output to the
requirement understanding, key ambiguities, recommended approach, main risks,
and validation method. The agent should not generate code or modify files
during this phase.

The workflow is deliberately simple:

1. Invoke `eval-exec` through the agent tool for the change request.
2. The agent inspects relevant evidence and returns an assessment only.
3. Review the conclusion, evidence, scope, risks, and validation plan.
4. Send the standalone command `execute` when the assessment is approved.
5. The agent implements the approved scope, validates it, and reports the
   result.

Invoking the skill automatically starts assessment. The user does not need to
append `evaluate` or `评估` to the request.

## Commands

Use the canonical English commands:

- Start assessment: invoke `eval-exec` through the agent tool.
- Approve implementation: `execute`.

Ambiguous confirmations such as `go ahead`, `continue`, `proceed`, `sounds
good`, `do it`, `好的`, `继续`, and `可以` are intentionally not treated as the
execution command. This keeps approval explicit across different agent
harnesses.

## What the assessment covers

The skill adapts its response to the request. Typical assessments include:

- the conclusion and current implementation state;
- concrete repository, runtime, configuration, log, or measurement evidence;
- prioritized recommendations and why their order matters;
- boundaries, security concerns, compatibility risks, and open questions;
- expected implementation scope and validation steps;
- the observable result users should expect.

The assessment phase is read-only. Execution is limited to the latest
unchanged assessment; a material scope change requires a new assessment.

## Installation

### Install through an Agent

Send the following message to your Agent:

> Please install this skill for me: [https://github.com/13ob0/skill-eval-exec](https://github.com/13ob0/skill-eval-exec)

The Agent will read the repository, find **eval-exec**, and complete the
installation. Once installed, it will be displayed as “Eval Exec”.

### Skills CLI

Install this skill from GitHub with the [`skills` CLI](https://www.skills.sh/docs/cli):

For a project-scoped installation:

```bash
npx skills add 13ob0/skill-eval-exec --skill eval-exec
```

For a global Codex installation:

```bash
npx skills add 13ob0/skill-eval-exec --skill eval-exec --global --agent codex
```

Both commands fetch the skill from GitHub, so the target commit must already
be pushed to the repository.

This repository follows the conventional top-level `skills/` layout, with the
skill at `skills/eval-exec`.

### Manual installation

Clone or download this repository, then copy `skills/eval-exec` into the
agent's skills directory. For Codex, the target is usually:

```text
$CODEX_HOME/skills/eval-exec
```

When `CODEX_HOME` is not set, use the default Codex skills directory for your
platform, commonly `~/.codex/skills/eval-exec`.

Start a new agent session after installation, then invoke `eval-exec` through
the agent tool.

## Development and packaging

The source of truth is `skills/eval-exec/SKILL.md`. Validate and package it
with the skill-creator scripts available in your agent environment:

```bash
python <skill-creator>/scripts/quick_validate.py skills/eval-exec
python <skill-creator>/scripts/package_skill.py skills/eval-exec dist
```

The packaged artifact contains the skill directory and its `SKILL.md`; README,
license, and repository metadata remain outside the installable skill.

## Compatibility and limits

- The workflow is designed for invocation through an agent tool.
- Automatic skill selection is host-specific and is not guaranteed by this
  repository.
- English is the primary instruction language; the literal Chinese commands
  `评估` and `执行` remain supported.
- The gate coordinates agent behavior but is not a substitute for sandbox,
  approval, access-control, or deployment safeguards.

## License

MIT. See [LICENSE](LICENSE).
