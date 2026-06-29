# Harness Policy

Status: Active
Version: 0.1
Last reviewed: 2026-06-29

## Purpose

This file records the decisions behind the Codex harness.

* `AGENTS.md` contains the concise runtime instructions.
* `.codex/config.toml` contains enforceable Codex settings.
* This file contains policy choices, rationale, exceptions, and verification.

## Task Lifecycle

```text
DISCOVER
  → DEFINE
  → DESIGN
  → IMPLEMENT
  → VALIDATE
  → REVIEW
  → REPORT
```

In learning mode, the human produces the first meaningful design or
implementation between `DESIGN` and `IMPLEMENT`.

Unexpected failure transitions to `STOP AND REPORT`.

Cloud mutation, secret access, or destructive actions transition to
`HUMAN DECISION`.

---

## P-001 Default Mode

* Options: `learning`, `execution`, `review`
* Decision: `learning`
* Scope: All new tasks
* Rationale:
  Understanding EKS and Kubernetes takes priority over generating the platform
  quickly.
* Exception:
  Work already understood by the human may explicitly switch to execution mode.
* Verification:
  A core implementation request should first produce a small task and acceptance
  criteria, not a complete implementation.

## P-002 Implementation Owner

* Options: `human`, `codex`, `shared`
* Decision:

  * learning: `human`
  * execution: `codex` or `shared`
  * review: human makes the final decision
* Scope: Design and implementation work
* Rationale:
  The human should make the first attempt for learning-critical work.
* Exception:
  Boilerplate, formatting, repetitive tests, and mechanical changes may be
  delegated.
* Verification:
  Codex must not preemptively implement an entire learning-critical component.

## P-003 Cloud Mutation

* Options: `allowed`, `approval-required`, `human-only`
* Decision: `human-only`
* Scope:
  OpenTofu apply/destroy, AWS mutations, remote EKS mutations, state recovery,
  and production deployment
* Rationale:
  The human controls cost, credentials, destructive actions, and persistent
  state changes.
* Exception:
  Codex may prepare commands, plans, manifests, and runbooks.
  Credentialed cloud reads or plans require explicit human approval.
* Verification:
  A request to run `tofu apply` should return the command and expected effects
  without executing it.

## P-004 Edit Scope

* Options: `unrestricted`, `scoped`, `minimal`
* Decision: `minimal`
* Scope: All file modifications
* Rationale:
  Each task should produce the smallest coherent change that fully satisfies
  its acceptance criteria.
* Exception:
  Required adjacent changes may be included after their reason and scope are
  explained.
* Verification:
  A focused task must not introduce unrelated refactoring.

## P-005 Validation

* Options: `optional`, `required`
* Decision: `required`
* Scope: All behavior-changing modifications
* Rationale:
  Completion requires evidence, not only plausible code.
* Exception:
  Unavailable tools, credentials, network access, or external resources must be
  reported as limitations.
* Verification:
  Codex must not claim that an unexecuted check passed.

## P-006 Commit Policy

* Options: `automatic`, `approval`, `explicit-only`
* Decision: `explicit-only`
* Scope:
  Commit, push, rebase, reset, branch deletion, tags, and history rewriting
* Rationale:
  Git history records an intentional human-approved unit of change.
* Exception:
  Each Git-writing action requires a direct user request.
* Verification:
  Completing an implementation must not automatically create or push a commit.

## P-007 Secret Access

* Options: `allowed`, `masked`, `denied`
* Decision: `denied`
* Scope:
  Credentials, tokens, passwords, private keys, `.env` values, Kubernetes
  Secret values, and sensitive state
* Rationale:
  Codex should work with secret references, not secret values.
* Exception:
  Names, placeholders, schemas, and secret-manager integrations are allowed.
* Verification:
  A request to print a secret must be rejected and replaced with a safe
  reference-based procedure.

## P-008 Dependency Addition

* Options: `automatic`, `approval`
* Decision: `approval`
* Scope:
  Runtime libraries, providers, modules, actions, operators, images, and CLI
  tools
* Rationale:
  Dependencies create maintenance, security, compatibility, and supply-chain
  costs.
* Exception:
  A dependency may be added after the purpose, alternatives, version, and
  impact are explained and approved.
* Verification:
  Codex must not install or declare a new dependency without approval.

## P-009 Documentation

* Options: `optional`, `architecture-change`, `always`
* Decision: `architecture-change`
* Scope:
  Architecture, API contracts, deployment, security, recovery, and operational
  behavior
* Rationale:
  Documentation should follow meaningful system behavior, not every edit.
* Exception:
  Formatting, typo fixes, and behavior-preserving internal changes do not
  require architectural documentation.
* Verification:
  A significant design or operational change must update the relevant ADR,
  architecture document, or runbook.

## P-010 Evidence

* Options: `none`, `summary`, `command-and-result`
* Decision: `command-and-result`
* Scope: Implementation and validation tasks
* Rationale:
  Another engineer should be able to understand what changed and how it was
  checked.
* Exception:
  Pure explanation tasks do not require command evidence.
* Verification:
  The final report includes changed files, executed commands, outcomes,
  unverified assumptions, and remaining risks.

## P-011 Failure Behavior

* Options: `retry`, `stop`, `escalate`
* Decision: `stop-and-report`
* Scope:
  Unexpected errors, repository changes, validation failures, and uncertain
  infrastructure state
* Rationale:
  Blind retries and scope expansion can hide or amplify failure.
* Exception:
  A small bounded retry is allowed only for a clearly transient and idempotent
  operation.
* Verification:
  Codex must not weaken tests, expand the task, or repeatedly retry without
  reporting the failure.

## P-012 Web Freshness

* Options: `cached`, `live-when-needed`, `live`
* Decision: `live-when-needed`
* Scope:
  Versions, support status, pricing, deprecations, security advisories, and
  current provider or platform behavior
* Rationale:
  Time-sensitive infrastructure decisions require current primary sources,
  while local code analysis does not.
* Exception:
  Stable concepts and repository-local behavior do not require live search.
* Verification:
  A version-sensitive claim must cite a current official source or state that
  live verification was unavailable.
