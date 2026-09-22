# ADR-0002: Simulation core is independent from presentation and platform services

Status: **Accepted**  
Date: 2026-09-22

## Context

Genetix depends on simulation depth, long-running worlds, deterministic debugging, large batch experiments, and future scalability.

Coupling ecological rules to UI, rendering, scene objects, ad SDKs, or platform APIs would make testing, balancing, optimization, and future engine changes unnecessarily difficult.

## Decision

The authoritative simulation core will be designed as an independent domain layer.

It must be possible to run the simulation without:

- rendering;
- UI;
- ad SDKs;
- analytics SDKs;
- mobile platform APIs.

Presentation consumes simulation state/events but does not own authoritative ecological state.

## Consequences

Positive:

- headless simulations and automated balance experiments;
- deterministic tests and replay debugging;
- easier profiling;
- cleaner future multithreading;
- reduced vendor/engine coupling;
- easier save-system reasoning.

Cost:

- requires deliberate boundaries and adapter code;
- may initially feel slower than directly wiring logic into visual objects.

The cost is accepted because these boundaries support the core product, not speculative infrastructure.
