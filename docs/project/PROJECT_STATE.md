# Project State

Last updated: 2026-09-22  
Foundation version: **0.1**

## Current phase

**Pre-production — Design Foundation**

No production engine or game code has been selected or committed yet.

## Current product definition

Genetix is a mobile autonomous ecosystem/evolution game.

Core rule:

> The player creates conditions. Life creates the result.

The world should continue to produce meaningful ecological and evolutionary change without player input.

## Confirmed requirements

- Mobile game.
- Long sessions.
- High replayability through systemic variation.
- Autonomous world simulation.
- Player influences the world indirectly.
- Every new session can unfold differently.
- Clean, understandable codebase.
- Architecture designed for long-term expansion.
- Simulation independent from presentation.
- Multithreading readiness, without premature parallelism.
- Persistent project context must live outside chat.
- GitHub is technical/design source of truth.
- The live Google Sheets human-facing project mirror is maintained alongside GitHub: https://docs.google.com/spreadsheets/d/11K6wY7k0JaI6m3tdTy2kPH582qdHVOjThzKkeH19kLk/edit
- Monetization direction: optional rewarded ads.

## Important current hypotheses

These are not yet final decisions:

- world may use a grid around 64×64 or an equivalent region representation;
- environment may use derived values such as elevation, temperature, moisture and fertility;
- populations should be aggregated by species/region;
- species should be represented by a compact trait vector;
- mutation + isolation + divergence should drive speciation;
- session history/tree of life should be a major emotional/legibility feature;
- "Influence" or an equivalent constrained resource may gate player interventions.

## Unknowns that must be resolved before serious implementation

- Minimum viable ecology formulas and stability rules.
- Validation of the proposed 100-year base tick through prototype behavior.
- Validation of 64×64 as the reference world size through performance/legibility tests.
- Minimum viable organism/species traits.
- Food/energy model.
- Population growth and carrying capacity model.
- Predation model.
- Migration/dispersal model.
- Mutation model.
- Speciation criteria.
- Extinction criteria.
- Player intervention economy.
- How the player understands causality.
- Engine/language choice.
- Target device performance envelope.
- Offline progression policy.
- Rewarded-ad reward design.

## Current milestone

### M0 — Design Foundation v0.1

Success means we can describe a 60–90 minute session from beginning to end and specify a minimal headless simulation model clearly enough to implement without inventing core rules while coding.

## Recently completed

- Session Arc v0.2 accepted and merged (PR #1).
- Core gameplay loop strengthened around prediction → indirect action/inaction → autonomous consequence.
- Systemic Threads introduced as a way to surface real emergent situations without scripted quests.

## Recently completed

- Session Arc v0.2 accepted and merged (PR #1).
- Simulation Model v0.1 and World & Time Scale v0.1 accepted and merged (PR #3).
- Reference model now uses configurable grid geography, sparse demes, deterministic staged updates, and a fixed 100-year initial tick hypothesis.

## Current design review

Draft document on branch `design/ecology-loop-v0.1`:

- `docs/design/ECOLOGY_LOOP.md`

Current ecology proposal:

- producer biomass → herbivores → predators;
- smooth climate suitability rather than hard biome gates;
- stable producer regeneration toward environmental capacity;
- deterministic resource competition;
- saturating predation with per-tick prey safety cap;
- bounded exponential population growth/decline;
- conservative staged migration flux;
- local/species extinction rules;
- pressure ledger for player-facing causality;
- systemic ecology Thread detectors;
- mostly deterministic first ecology prototype;
- controlled micro-scenarios and headless metrics before full procedural balancing.

## Next work

1. Review Ecology Loop v0.1.
2. Define Evolution & Speciation v0.1.
3. Define player interventions and Influence constraints.
4. Define prototype success metrics and batch-run metrics across ecology + evolution.
5. Only then evaluate engine/language options against requirements.

## Technical debt

None yet.

## Active blockers

None.

## Repository protocol

Meaningful future implementation work should normally use branches and pull requests. Direct-to-main writes are acceptable only for repository bootstrap or urgent documented maintenance.
