# Genetix Roadmap

Status: **Draft v0.1**

The roadmap is outcome-based. Dates are intentionally not committed before scope and implementation velocity are known.

## M0 — Design Foundation

Goal: establish a coherent, testable design before selecting irreversible implementation details.

Deliverables:

- 60–90 minute target session arc;
- minimum world model;
- minimum ecology model;
- minimum evolution/speciation model;
- player intervention model;
- causality/legibility model;
- prototype success metrics;
- engine/language evaluation criteria.

Exit condition:

> We can write the headless prototype specification without inventing major gameplay rules during implementation.

## M1 — Headless Ecology Prototype

Goal: prove that autonomous ecological dynamics can remain stable, variable, and interesting.

Capabilities:

- seeded world generation;
- environment state;
- producer/resource model;
- aggregated populations;
- feeding/predation;
- reproduction/death;
- migration;
- deterministic ticks;
- batch simulation;
- basic metrics and logs.

Exit condition:

> Large batches of seeded worlds produce multiple stable and unstable ecological histories for explainable reasons rather than immediately converging to the same state or universal extinction.

## M2 — Evolution Prototype

Goal: make populations adapt and branch over long simulation periods.

Capabilities:

- heritable trait variation;
- selection pressure;
- geographic/ecological isolation;
- divergence;
- speciation;
- extinction;
- lineage/tree-of-life tracking;
- deterministic replay tests.

Exit condition:

> Different worlds generate meaningfully different lineages, and important adaptations/speciation events are explainable.

## M3 — First Playable World

Goal: turn the headless simulation into an understandable interactive mobile experience.

Capabilities:

- map visualization;
- time controls;
- species inspection;
- population/biome overlays;
- timeline/history;
- first player interventions;
- basic save/load;
- debug/inspection UI.

Exit condition:

> A new player can identify what is happening and cause indirect ecological consequences without needing developer tools.

## M4 — Core Game Loop Validation

Goal: sustain a compelling 60–90 minute session.

Focus:

- intervention economy;
- progression during a world session;
- goals/motivation without destroying sandbox freedom;
- discovery;
- pacing;
- meaningful setbacks;
- long-term species attachment;
- readable causality.

Exit condition:

> Repeated playtests show players voluntarily continue sessions and can recount distinct stories from different worlds.

## M5 — Production Foundation

Goal: harden the project for long-term development.

Includes:

- finalized engine/project structure;
- performance profiling;
- selective parallelization where justified;
- versioned saves + migration framework;
- analytics abstraction;
- rewarded ads abstraction;
- content pipeline;
- CI/testing;
- crash/error reporting strategy;
- release build process.

## M6 — Content & Polish

Goal: increase breadth without compromising systemic clarity.

Potential areas:

- richer plant strategies;
- more feeding niches;
- seasonal behavior;
- diseases/parasites;
- symbiosis;
- oceans;
- catastrophes;
- geological change;
- expanded interventions;
- visual identity and audio.

Each addition requires a gameplay justification and performance budget.

## M7 — Soft Launch / Live Validation

Goal: validate retention, pacing, performance, monetization, and stability with real players.

Rewarded ads remain optional and must not be required for a functional enjoyable core experience.

## Long-term expansion candidates

Not commitments:

- deeper aquatic ecosystems;
- flight;
- advanced social behavior;
- complex disease ecology;
- planetary cycles;
- multiple planets/world archetypes;
- very long-lived persistent worlds;
- advanced procedural organism presentation;
- intelligent life/civilization only if it fits the original autonomous-world fantasy.
