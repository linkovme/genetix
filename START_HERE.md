# START HERE — Genetix Project Handoff

This file is the mandatory entrypoint for any new ChatGPT session or developer joining Genetix.

## 1. What Genetix is

Genetix is a mobile game about an autonomous evolving ecosystem.

The player does **not** directly control animals, populations, or species. The player changes environmental conditions and applies limited interventions. The simulation decides the consequences.

The central fantasy is:

> **I changed the world, and life responded in a way I did not script.**

A successful session should create memorable emergent history: migrations, adaptation, speciation, collapses, recoveries, extinction, and ecological succession.

## 2. Product principles

1. **The world lives without the player.**
   The simulation must remain interesting and coherent while the player observes.

2. **The player creates conditions; life creates outcomes.**
   Avoid direct "spawn the desired species" solutions where systemic intervention can produce the result.

3. **Replayability comes from interacting systems, not handcrafted level volume.**
   Seeds, geography, climate, organism traits, ecology, and player intervention should create divergent histories.

4. **Complexity must create gameplay.**
   A feature is not justified only because it is realistic.

5. **Long sessions, simple implementation.**
   Prefer systems with high combinatorial output per unit of code/content.

## 3. Engineering principles

- Repository is the source of truth; chats are not.
- Simulation core is independent from rendering, UI, ads, and engine-specific code.
- Determinism is a first-class requirement wherever practical.
- Simulation logic must be testable headlessly.
- Population simulation is aggregated; visual creatures are representations, not authoritative simulation entities.
- Data-driven content is preferred to hardcoded special cases.
- Save data is versioned and migratable.
- Multithreading capability is designed into boundaries, but parallelism is added only after profiling.
- Temporary shortcuts must be explicit technical debt, never hidden permanent architecture.
- A competent mid-level developer should be able to understand the project structure without reverse-engineering accidental coupling.

Read: [docs/architecture/ENGINEERING_PRINCIPLES.md](docs/architecture/ENGINEERING_PRINCIPLES.md)

## 4. Monetization

Primary monetization direction: **rewarded ads**.

Ads must be optional and reward the player rather than deliberately creating pain that is sold back as relief. Ad-provider SDK code must be isolated behind an adapter/interface so monetization providers can be changed without modifying simulation/gameplay systems.

Detailed design is intentionally deferred until the core loop is validated.

## 5. Current project phase

Read [docs/project/PROJECT_STATE.md](docs/project/PROJECT_STATE.md) before doing work.

At project bootstrap, the priority is **not** production code. The priority is to define and validate:

- world model,
- time model,
- population model,
- food/energy relationships,
- migration,
- mutation,
- speciation,
- extinction,
- player intervention,
- conditions for an interesting 60–90 minute session.

## 6. Working protocol for a new ChatGPT session

Before proposing or changing implementation:

1. Read this file.
2. Read `PROJECT_STATE.md`.
3. Read the relevant design/architecture documents linked from it.
4. Check recent repository changes if needed.
5. Treat repository documents as authoritative over remembered chat context.
6. If repository docs disagree, surface the conflict and resolve it explicitly.
7. After a meaningful design or implementation decision, update the relevant documentation.
8. After a meaningful work session, update `PROJECT_STATE.md`.

Do not reconstruct missing facts from assumptions when they can be read from this repository.

## 7. Human-facing project mirror

A Google Sheets project dashboard is planned as the human-readable operational mirror: roadmap, current sprint, backlog, systems, balance, decisions, bugs, risks, monetization, and versions.

GitHub remains authoritative for technical/design truth. The Sheet exists to make project status easy to understand at a glance.

## 8. Immediate next step

Complete **Design Foundation v0.1** before choosing the engine:

- define the 60–90 minute session arc;
- define the minimum simulation entities and state;
- define the update/tick model;
- define what produces speciation and extinction;
- define the first player interventions;
- define measurable prototype success criteria.

See [docs/project/ROADMAP.md](docs/project/ROADMAP.md).
