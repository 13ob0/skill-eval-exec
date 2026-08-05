# eval-exec

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

### Skills CLI

If your agent supports the [Skills CLI](https://github.com/mattpocock/skills),
install from the GitHub repository with:

```bash
npx skills@latest add <owner>/<repository>
```

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
