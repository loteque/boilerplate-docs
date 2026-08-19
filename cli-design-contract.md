# CLI Design Contract Template

Status: **Design-phase template**

Contract: `<project>-<cli>-design/1`

> Replace angle-bracket placeholders and adapt or remove sections that do not apply. This template defines how a CLI is designed; it should not prematurely settle design questions that belong in the design phase.

## Purpose

This contract governs the design phase for `<cli>`, a command-line interface for `<system/purpose>`.

It defines fixed invariants, open design questions, required design outputs, and acceptance gates. The design phase MUST resolve implementation-blocking questions before implementation authority is granted.

## Core principle

> **Primitives first; UX later.**

The CLI is an interface over stable operations/primitives. It is not where domain semantics, authority, policy, or source-of-truth rules are invented.

Every state-changing capability exposed by the CLI SHOULD first exist as an independently specified and testable operation.

## Target interfaces

Identify every interface expected to consume the same primitive substrate.

```text
                    deterministic primitives
                             │
              ┌──────────────┴──────────────┐
              │                             │
          Unix-like CLI                <other adapter>
          composable                   API / agent / GUI
          commands                     / automation
```

No domain-semantic operation SHOULD exist only as interactive terminal behavior when noninteractive or programmatic use is required.

## Fixed invariants

The design phase MUST preserve these invariants unless they are explicitly superseded by the authority that owns them.

### Domain and authority

1. `<invariant>`
2. `<invariant>`
3. The CLI MUST NOT gain authority merely because it orchestrates an operation.
4. Presentation and orchestration MUST NOT silently redefine domain semantics.

### Ownership

1. `<component/state>` is owned by `<owner>`.
2. `<component/state>` is owned by `<owner>`.
3. Generated, cached, derived, and authoritative state MUST be distinguishable.

### Distribution and locality

1. `<installation/runtime invariant>`
2. `<network/locality invariant>`
3. `<version/update invariant>`

### State and evidence

1. Unknown or conflicting state MUST fail closed unless a documented recovery operation applies.
2. Operational provenance MUST be distinguishable from domain/canonical state.
3. Destructive or authority-bearing mutations MUST NOT occur through inference or hidden defaults.

## Primitive dependency rule

The CLI may orchestrate only operations whose semantics are sufficiently specified and tested for their risk level.

Expected primitive families may include:

```text
installation
initialization/bootstrap
configuration
state inspection
validation
planning/preflight
mutation/application
execution
persistence
verification
recovery/migration
```

A missing primitive is a design or implementation gap. It MUST NOT be hidden as ad hoc CLI behavior.

## Command design direction

Commands SHOULD follow Unix-like expectations where practical:

- composable operations;
- explicit inputs;
- useful exit codes;
- stdout for requested/results output and stderr for diagnostics;
- noninteractive operation for automation;
- stable machine-readable output where programmatic consumption is expected;
- idempotence where the operation permits it;
- predictable `--help` behavior;
- no semantic difference between interactive and noninteractive execution of the same operation.

Interactive helpers MAY collect inputs or explain choices, but MUST delegate final semantics to the same underlying operations used by noninteractive callers.

## Mutation model

For consequential mutations, evaluate a separation such as:

```text
inspect/list
    ↓
plan/preflight       # validate and show proposed effect; no mutation
    ↓
apply/execute        # explicit mutation
    ↓
verify/status        # confirm resulting state
```

The design phase MUST determine which operations require this separation and which are safely atomic.

## State-machine direction

If the CLI coordinates a lifecycle, define candidate states and transitions here without treating them as accepted merely because they appear in this template.

```text
<STATE_A>
   ↓
<STATE_B>
   ↓
<STATE_C>
```

Blocked, conflicted, incompatible, interrupted, and recovery states MUST be designed explicitly when applicable.

## Open design questions

The design phase MUST resolve at least the applicable questions below:

1. Exact command/subcommand topology and naming.
2. Primitive inventory and dependency graph.
3. Exact lifecycle states and legal transitions.
4. Which actions are read-only, automatic, confirmable, or explicitly authorized.
5. Input precedence: arguments, stdin, environment, configuration, defaults.
6. Human-readable versus machine-readable output contracts.
7. Exit-code taxonomy and error classification.
8. Idempotence, replay, interruption, rollback, and partial-failure behavior.
9. Concurrency and locking semantics for mutable state.
10. Filesystem ownership, path resolution, symlink handling, and traversal boundaries.
11. Configuration ownership, schema, migration, and compatibility rules.
12. Version negotiation and upgrade/downgrade behavior.
13. Network-access boundaries and offline behavior.
14. Authentication, authorization, credentials, and secret-handling boundaries where applicable.
15. Logging, provenance, audit, and diagnostic requirements.
16. Extension/plugin boundaries and compatibility requirements where applicable.
17. Interactive UX behavior and its exact mapping to noninteractive primitives.
18. Automation/agent/API consumption requirements.
19. Platform portability and shell/environment assumptions.
20. Testing strategy for each primitive and composed command workflow.

Add project-specific questions required to eliminate implementation ambiguity.

## Required design outputs

The design phase SHOULD produce implementation-ready artifacts covering the applicable areas:

- lifecycle/state-machine specification;
- primitive inventory and dependency graph;
- command topology;
- input/configuration contract;
- output/result contract;
- exit-code and error taxonomy;
- mutation/preflight semantics;
- state/storage ownership;
- recovery/migration behavior;
- compatibility/versioning rules;
- security/authority invariants;
- noninteractive/automation contract;
- interactive composition design;
- conformance-test plan for each primitive;
- end-to-end CLI test plan.

Use diagrams and tables where they materially reduce ambiguity.

## Primitive contract checklist

For each primitive, specify:

| Concern | Required decision |
|---|---|
| Purpose | What single operation does it perform? |
| Inputs | What explicit data/state does it consume? |
| Outputs | What stable result does it emit? |
| Ownership | Which state may it read or mutate? |
| Authority | What permission, if any, is required? |
| Determinism | What behavior must be repeatable? |
| Idempotence | What happens on replay? |
| Conflicts | What conditions fail closed? |
| Mutation | What exact state can change? |
| Recovery | What happens after interruption/partial failure? |
| Exit status | How is success/failure represented to a shell? |
| Tests | What proves the contract? |

## CLI composition checklist

For each public command, specify:

| Concern | Required decision |
|---|---|
| Syntax | Command, subcommand, arguments, flags |
| Primitive mapping | Which primitive(s) does it invoke? |
| Input precedence | CLI/stdin/env/config/default ordering |
| Output | Human and machine-readable forms |
| Side effects | Exact mutations/network effects |
| Confirmation | Whether explicit confirmation is required |
| Exit codes | Stable shell-visible result |
| Piping | stdin/stdout composition expectations |
| Interactivity | Optional prompts and noninteractive equivalent |
| Failure | Diagnostics and recovery guidance |

## Design acceptance gates

A design decision becomes implementation authority only when:

1. it is consistent with all fixed invariants;
2. inputs, outputs, ownership, and failure semantics are explicit;
3. authority and mutation boundaries are explicit;
4. deterministic behavior is specified where applicable;
5. noninteractive callers can use the operation without changing its semantics;
6. required conformance tests can be stated before implementation;
7. unresolved alternatives affecting correctness are decided or explicitly deferred behind a non-blocking boundary;
8. the resulting design is recorded as durable project evidence.

Upper-layer convenience UX SHOULD NOT be implemented while required lower-level primitives remain undefined or unproven.

## Design-phase completion

The design phase is complete when:

- implementation-blocking open questions are resolved;
- required primitive contracts are implementation-ready;
- command composition is mapped to those primitives;
- failure/recovery semantics are explicit;
- test gates are defined;
- remaining deferred questions do not alter the correctness of the approved implementation boundary.

At that point, the approved design artifacts become the basis for implementation and conformance testing.
