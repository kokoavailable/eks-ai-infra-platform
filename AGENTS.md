# Repository Instructions

## Mission

Build and understand an EKS-based AI inference platform.

A working result is not sufficient unless the architecture, failure modes, and
validation are explainable.

## Operating Modes

The default mode is `learning`.

* Learning: explain the model and failure conditions, define one small task,
  and let the human make the first meaningful attempt.
* Execution: edit files only after the user explicitly requests execution mode.
* Review: inspect and report findings without editing unless requested.

Do not switch modes implicitly.

Read `.codex/configuration.md` only when changing the harness or resolving a
policy ambiguity.

## Ownership

For learning-critical work, the human owns the first design and implementation.

Codex may handle boilerplate, formatting, repetitive tests, and mechanical work.

## Safety Boundaries

Do not execute commands that create, modify, or delete cloud resources.

Human-controlled actions include:

* `tofu apply` and `tofu destroy`
* `terraform apply` and `terraform destroy`
* AWS or remote EKS mutations
* remote-state modification or recovery
* production deployment
* secret creation, rotation, reading, or disclosure
* commit, push, rebase, reset, force push, or destructive Git operations

Codex may prepare commands, plans, manifests, and runbooks.

Credentialed cloud reads or plans require explicit approval.

## Change Rules

* Make the smallest coherent change that satisfies the task.
* Do not perform unrelated refactoring.
* Preserve human-written code unless a change is necessary.
* Stop and report unexpected repository changes.
* Do not add dependencies without approval.
* Update documentation when architecture, API, deployment, security, recovery,
  or operational behavior changes.

## Repository Boundaries

* `infra/`: AWS and EKS desired state
* `deploy/`: Kubernetes workload desired state
* `cmd/`, `internal/`: application code
* `tests/`: expected behavior and regression tests
* `scripts/`, `Makefile`: repeatable commands
* `docs/adr/`: architecture decisions
* `docs/runbooks/`: operational procedures
* `docs/postmortems/`: failure analysis
* `.codex/`: harness configuration and policy

## Validation

Inspect the `Makefile` first and prefer repository-provided targets.

Run applicable checks:

* OpenTofu: `tofu fmt -check -recursive`, `tofu validate`, `tflint`,
  `trivy config .`
* Go: `go test ./...`, `go vet ./...`
* Kubernetes: `kustomize build deploy/kubernetes/base`,
  `kubeconform -strict -summary`

Do not claim a check passed unless it was executed.

Do not weaken or remove tests to make a change pass.

If validation cannot run, report the reason and the unverified scope.

## Freshness

Use current official documentation for versions, support status, pricing,
deprecations, security advisories, and current platform behavior.

If live verification is unavailable, state that limitation instead of guessing.

## Failure Handling

On an unexpected failure:

1. stop expanding the task
2. report the failed step and relevant error
3. report actions already attempted
4. propose the smallest safe next action

Retry only when the operation is clearly transient, bounded, and idempotent.

## Completion Report

Report:

* files changed
* commands executed
* validation outcomes
* assumptions and unverified items
* remaining risks
