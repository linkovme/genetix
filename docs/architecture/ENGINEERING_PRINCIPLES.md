# Engineering Principles

Status: **Accepted v0.1**

This document is the engineering constitution for Genetix. Deviations must be conscious and documented.

## 1. Repository over chat memory

GitHub is the durable source of truth. Important decisions, current state, architecture, design rules, and known debt must live in the repository.

A new developer or AI session should be able to recover project context from repository documentation.

## 2. Simulation core is independent

The authoritative simulation layer must not depend directly on:

- rendering;
- mobile UI;
- animation;
- rewarded-ad SDKs;
- analytics SDKs;
- platform APIs;
- engine scene objects.

The simulation should be executable headlessly for tests, balance runs, and long-duration simulation.

## 3. Determinism first

Given equivalent:

- seed/random stream state;
- simulation version;
- starting state;
- ordered player inputs;

the simulation should reproduce equivalent results wherever practical.

Randomness must flow through explicit controlled random sources rather than arbitrary global RNG usage.

## 4. Aggregation over individual simulation

A displayed population of 100,000 organisms does not imply 100,000 authoritative runtime entities.

Authoritative ecological state should generally operate on aggregated populations/regions. Visual agents may sample or represent those populations.

## 5. Data-driven systems

Prefer declarative configuration for:

- species archetypes;
- traits;
- tuning constants;
- environment parameters;
- intervention definitions;
- content tables.

Avoid proliferating one-off conditionals for individual content items.

## 6. Clean boundaries

Modules should have clear ownership and small public APIs.

Expected conceptual boundaries include:

- Simulation Core
- World/Environment
- Ecology/Population
- Evolution/Speciation
- Time/Simulation Scheduling
- Game Rules / Player Intervention
- Persistence
- Presentation
- Platform Services
- Monetization
- Analytics/Telemetry
- Developer Tools

These boundaries are conceptual; implementation language/engine is not yet selected.

## 7. Testability

Critical deterministic rules should be unit-testable without launching the game UI.

The project should eventually support:

- unit tests for formulas and invariants;
- deterministic replay tests;
- save/load round-trip tests;
- migration tests for save versions;
- long-run simulation soak tests;
- statistical/balance batch simulations;
- performance benchmarks.

## 8. Performance by measurement

Architecture should allow scalable execution, including future parallelism, but performance optimizations must be driven by profiling.

Do not introduce complex concurrency into core rules before the deterministic single-threaded model is correct and measurable.

## 9. Multithreading readiness

Avoid hidden mutable global state and uncontrolled cross-system writes.

Prefer simulation stages with explicit inputs/outputs so independent work can later be parallelized safely where profiling justifies it.

Deterministic execution and correctness take priority over early parallelism.

## 10. Save compatibility

Save data must have an explicit schema/version.

Breaking changes require migration strategy or an explicitly documented compatibility decision.

Long-lived player worlds are a core product expectation.

## 11. Technical debt is visible

No silent "temporary" hacks.

If a compromise is necessary:

- identify it;
- document why;
- describe risk;
- record cleanup trigger or target milestone.

## 12. Readability standard

Optimize for a competent mid-level developer being able to understand the codebase.

Prefer boring, explicit, maintainable code over clever abstractions.

## 13. YAGNI with reversible design

Do not build speculative systems merely because they may be useful years later.

Do establish stable boundaries where future change would otherwise be disproportionately expensive.

## 14. Monetization isolation

Rewarded advertising and provider SDKs must be isolated behind project-owned interfaces/adapters.

Gameplay grants a reward through a project-defined transaction. Provider callbacks must not directly mutate arbitrary game state.

Reward flow must be idempotent and resilient to duplicated callbacks, failures, cancellation, and connectivity loss.

## 15. Architecture decisions are recorded

Meaningful technical choices use ADRs in `docs/adr/`.

An ADR should capture:

- context;
- decision;
- alternatives;
- consequences;
- status.

This keeps future maintainers from "simplifying" an intentional constraint without knowing why it exists.
