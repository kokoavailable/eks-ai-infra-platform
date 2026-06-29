# T0 - Codex Harness Policy

Date: 2026-06-29
Status: Complete
Mode: learning
Owner: human

## Goal

Define how Codex should participate in this repository before building EKS
infrastructure.

The purpose of this task was not to create infrastructure. The purpose was to
make the working rules explicit so that later EKS, OpenTofu, AWS, and
Kubernetes work happens inside a controlled learning workflow.

## Completed

- Created `AGENTS.md`.
- Created `.codex/config.toml`.
- Created `.codex/configuration.md`.
- Defined the default collaboration mode as `learning`.
- Defined the implementation owner policy.
- Defined the cloud mutation boundary as `human-only`.
- Documented extended harness policy decisions from P-001 to P-012.

## Key Decisions

### Learning before automation

The project should optimize for understanding EKS and Kubernetes, not for
quickly generating a finished platform. In learning mode, the human should make
the first meaningful design or implementation attempt.

### Codex as reviewer and guide

Codex can explain, review, validate, suggest small tasks, and handle repetitive
or mechanical work. Codex should not preemptively implement the full
learning-critical path.

### Cloud mutation is human-only

Codex must not run cloud-mutating commands such as:

- `tofu apply`
- `tofu destroy`
- AWS-changing commands
- remote EKS-changing commands

Codex may prepare commands, plans, runbooks, and validation steps, but the
human controls actions that can create cost, delete resources, or mutate remote
state.

### Evidence matters

Implementation and validation tasks should leave evidence. A task is stronger
when it records changed files, commands run, command results, remaining gaps,
and risks.

## Evidence

- `AGENTS.md` contains the concise runtime collaboration rules.
- `.codex/config.toml` sets live web search behavior.
- `.codex/configuration.md` records the fuller policy rationale, exceptions,
  and verification checks.
- `ROADMAP.md` provides the project direction and first vertical slice target.

## Review Notes

What went well:

- The task created guardrails before touching cloud infrastructure.
- The learning mode is explicit.
- Cloud mutation risk is controlled early.
- The policy separates fast execution from learning-critical implementation.

Risks and gaps:

- The repository still has no `README.md`.
- The project directory skeleton has not been created yet.
- No OpenTofu code exists yet.
- No local gateway code exists yet.
- The current files are still uncommitted.

## Next Task

T1 should define project scope and create the repository bootstrap.

Task name:

```text
T1 - Project Scope and Repository Bootstrap
```

Scope:

- `README.md`
- `docs/project-scope.md`
- `app/gateway/`
- `infra/envs/lab/`
- `infra/modules/`
- `deploy/`
- `observability/`
- `experiments/`
- `docs/adr/`
- `docs/runbooks/`
- `scripts/`

Out of scope:

- OpenTofu implementation
- EKS provider or module code
- Go gateway implementation
- AWS commands
- Cloud mutation

Acceptance criteria:

- `README.md` explains the project goal, Phase 0 target, and safety rules.
- `docs/project-scope.md` defines what the project will build, what it will not
  build, and how Phase 0 success will be judged.
- The repository skeleton matches the initial monorepo structure from
  `ROADMAP.md`.
- Empty directories that must remain in git include `.gitkeep`.
- `git status --short` shows only intentional files.
- No cloud command is executed.

## Suggested Commit

```text
docs: define codex harness and t0 log
```
