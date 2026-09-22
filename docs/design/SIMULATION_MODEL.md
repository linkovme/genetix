# Simulation Model v0.1

Status: **Draft for product/technical review**

This document defines the minimum authoritative state model for Genetix. It is deliberately smaller than the long-term vision. The purpose is to create a simulation that is understandable, testable, deterministic, and extensible without simulating individual animals.

## 1. Core rule

> The simulation owns the world. Presentation only observes it.

The authoritative simulation must be runnable headlessly with no rendering, UI, ads, analytics, or engine scene objects.

## 2. State categories

Every piece of runtime data belongs to one of three categories.

### Authoritative state

Data that changes the future simulation result and therefore belongs in saves/replays.

Examples:

- environment values;
- species identities and lineage;
- population abundance;
- local evolving traits;
- active world modifiers;
- simulation clock;
- controlled RNG state.

### Derived state

Data that can be recomputed from authoritative state.

Examples:

- biome labels;
- climate suitability score;
- carrying-capacity estimate;
- pressure summaries;
- gene-flow clusters;
- Thread candidates;
- map overlays.

Derived state should not become authoritative merely because it is convenient for UI.

### Presentation state

Data that has no ecological authority.

Examples:

- camera position;
- selected species;
- animated creature proxies;
- particles;
- open panels;
- map interpolation.

Presentation state must never directly mutate ecological data.

---

## 3. Root state

### SimulationState

The single authoritative root of one world.

Owns references/collections for:

- SimulationClock
- WorldGrid
- SpeciesRegistry
- DemeStore
- LineageGraph
- ActiveEnvironmentModifiers
- DivergenceState
- WorldHistory
- RandomStreams

It does **not** own:

- Influence balance;
- pinned Threads;
- ad state;
- UI state.

Those belong to the game/session layer.

---

## 4. Simulation entities

### 4.1 SimulationClock

Owns:

- current tick;
- accumulated simulated years;
- scheduler/cadence counters;
- simulation version.

There is exactly one clock per world.

### 4.2 WorldGrid

Owns:

- dimensions;
- topology configuration;
- stable CellIds;
- contiguous environmental storage;
- chunk partition metadata used for execution.

The grid is logical simulation geography, not rendering tiles.

### 4.3 WorldCell

A stable location in the world.

Minimum authoritative environmental state:

- elevation;
- land/water flag;
- baseline temperature component;
- current temperature;
- moisture;
- fertility/productivity;
- producer biomass;
- active local disturbance intensity.

Candidate derived values:

- biome name;
- climate classification;
- traversability;
- resource regeneration rate;
- habitat quality for a given trait profile.

A WorldCell does not contain objects representing individual animals.

### 4.4 Species

A lineage identity shared by all living demes belonging to the same species.

Owns:

- SpeciesId;
- parent SpeciesId, if any;
- origin tick/year;
- extinction tick/year, if extinct;
- lineage metadata;
- feeding strategy/guild;
- species-level immutable or slowly changing descriptors;
- naming/presentation seed.

A Species does **not** own one global population number.

A Species does **not** own one global mutable trait vector, because populations in different regions must be able to diverge before speciation.

### 4.5 Deme

A **deme** is the minimum authoritative population unit: one species occupying one logical cell.

Key:

> (SpeciesId, CellId) must be unique.

Minimum state:

- abundance;
- local mean heritable trait vector;
- local trait variance/variation budget;
- recent migration inflow/outflow summary;
- recent birth/death summary;
- recent feeding/resource pressure summary;
- optional low-population/extinction grace state.

A species range is therefore the set of cells containing its living demes.

This is the central scaling choice of Genetix:

> 80,000 displayed animals may be represented by dozens of demes, not 80,000 simulation entities.

Demes are stored sparsely. Empty (species, cell) combinations do not consume full entity state.

### 4.6 TraitProfile

A compact value object, not an entity.

Initial trait categories may include:

- body size;
- mobility/dispersal;
- reproduction tendency;
- metabolic demand;
- thermal optimum;
- thermal tolerance;
- moisture optimum;
- moisture tolerance;
- foraging/attack efficiency;
- defense/evasion;
- diet/prey preference parameters.

Exact traits are not locked by this document.

Rules:

- the authoritative evolving trait mean exists at deme level;
- a species-level displayed profile is a population-weighted aggregation;
- mutation and selection change deme-level values gradually;
- migration exchanges both individuals and trait information.

### 4.7 ResourceState

For the first ecology prototype, primary producers are represented as environmental biomass rather than full species.

This state is owned by WorldCell.

Initial model:

- producer biomass;
- regeneration/productivity potential;
- local consumption pressure.

This is intentionally replaceable later by explicit plant species or multiple resource channels.

### 4.8 EnvironmentModifier

A time-bounded or persistent change to world conditions.

Examples:

- rainfall increase;
- warming/cooling;
- drought;
- wildfire recovery;
- volcanic fertility change;
- terrain/elevation transition.

Owns:

- modifier id;
- affected cells/region selector;
- start tick;
- duration or persistence;
- intensity curve;
- modifier type;
- source: player, procedural world process, or future system.

Player interventions should generally create or alter EnvironmentModifiers rather than directly editing biological state.

### 4.9 LineageGraph

Stores durable ancestry.

Owns:

- parent/child species relationships;
- split timestamps;
- extinction timestamps;
- origin summaries.

The lineage graph remains available after extinction.

### 4.10 DivergenceState

Persistent evolutionary bookkeeping used to decide whether separated populations have become distinct species.

It may track:

- stable isolation candidates;
- duration of low gene flow;
- trait-distance history;
- involved demographic clusters;
- continuity across speciation evaluation passes.

Important:

The exact divergence algorithm is not fixed here.

The architecture requirement is that speciation is derived from **population separation + heritable divergence over time**, not from a random independent event.

### 4.11 WorldEvent

A durable historical record emitted only for notable changes.

Examples:

- species origin;
- extinction;
- major migration;
- major range contraction;
- player intervention;
- major environmental transition.

WorldEvents contain measured context sufficient for later explanation.

They do not store every simulation tick.

---

## 5. Game/session-layer entities

These are saved with the player's world but do not belong to the autonomous ecological core.

### 5.1 GameSessionState

Owns:

- Influence balance/capacity;
- intervention unlocks;
- pinned Threads;
- player-facing progression;
- optional tutorial/onboarding state.

### 5.2 InterventionCommand

A validated command from the game layer to the simulation.

Examples:

- change moisture target in region;
- apply temperature modifier;
- schedule terrain uplift;
- create a temporary disturbance.

The command becomes simulation-owned state only after validation and conversion into a deterministic simulation operation.

### 5.3 ThreadState

Threads surface interesting simulation situations.

A Thread may store:

- detector type;
- referenced species/cells;
- start tick;
- current status;
- player pinned/unpinned flag;
- resolution state.

Crucial rule:

> Threads observe the simulation. They do not alter it.

Most Thread meaning should be reproducible from measured state. Only continuity/pinning/history needs persistence.

---

## 6. Ownership map

| Data | Authoritative owner |
| --- | --- |
| Temperature/moisture/fertility | WorldCell / environment subsystem |
| Producer biomass | WorldCell / resource subsystem |
| Species ancestry | LineageGraph |
| Species identity | SpeciesRegistry |
| Population abundance | Deme |
| Local evolving traits | Deme |
| Species-wide displayed traits | Derived aggregate |
| Range | Derived from living demes |
| Migration flux | Tick-stage transient + recent summary on Deme |
| Gene-flow cluster | Derived evolutionary analysis |
| Speciation continuity | DivergenceState |
| Timeline/history | WorldHistory |
| Influence | GameSessionState |
| Thread detection | Derived |
| Pinned/resolved Thread continuity | ThreadState |
| Animated animals | Presentation only |

---

## 7. Update architecture

The simulation uses a deterministic staged pipeline.

Systems do not freely mutate one another's state in arbitrary order.

Conceptual tick:

1. apply scheduled environment modifiers;
2. update environmental values/resources;
3. calculate feeding and predation demand;
4. resolve consumption;
5. calculate births/deaths;
6. calculate migration flux;
7. commit migration/gene flow;
8. update heritable trait statistics;
9. remove extinct demes/species when rules permit;
10. evaluate divergence/speciation on its configured cadence;
11. emit notable WorldEvents;
12. evaluate Thread detectors;
13. advance clock.

Exact formulas and some stage ordering may change during M0/M1, but three architectural constraints are fixed:

- cross-cell writes are staged, not performed ad hoc;
- deterministic ordering is explicit;
- systems consume stable input for a stage and commit outputs at a barrier.

This keeps the model understandable and creates a clean path to future multithreading.

---

## 8. Double-buffer / delta principle

For state that can be affected by many cells or species in one tick, systems calculate **deltas/fluxes** first and apply them after all relevant calculations finish.

Example: migration.

Bad:

> Cell A immediately subtracts animals and adds them to Cell B while Cell B is simultaneously being processed.

Preferred:

1. calculate all A → B migration fluxes from the same stage snapshot;
2. sort/reduce them deterministically;
3. apply all resulting population and gene-flow changes.

The same principle applies to predation/resource consumption when multiple species compete for the same resource pool.

---

## 9. Stable IDs

Simulation entities use stable numeric/internal IDs.

Required categories:

- CellId
- SpeciesId
- DemeId or deterministic (SpeciesId, CellId) key
- ModifierId
- WorldEventId
- ThreadId where persistence is needed

Player-facing names are presentation metadata and are never identity.

---

## 10. Core invariants

The simulation should assert these in tests/debug builds.

- population abundance cannot be negative;
- producer biomass cannot be negative;
- each live Deme references one valid live-or-historic Species;
- only one Deme exists for a given (SpeciesId, CellId);
- extinct Species cannot spontaneously acquire a new Deme without an explicit future resurrection mechanic;
- lineage graph has no cycles;
- simulation time never moves backward;
- one deterministic input state + seed + command stream produces reproducible output;
- Thread/UI state cannot modify ecological state without a validated InterventionCommand.

---

## 11. Why this model scales

The cost grows primarily with **occupied species-cell pairs**, not theoretical animal count.

Example:

- 4,096 cells;
- 50 species;
- average species occupies 120 cells;
- ~6,000 living demes.

That is dramatically cheaper than simulating hundreds of thousands or millions of individual organisms.

Long-term optimization paths remain open:

- chunk-level parallelism;
- sparse active-cell iteration;
- species-local processing;
- data-oriented contiguous arrays;
- batched headless execution;
- simulation LOD for extremely long fast-forward periods if ever required.

None of these require changing the player-facing game model.

---

## 12. Deliberately absent entities

The first simulation model does **not** require:

- IndividualAnimal
- DNASequence
- Egg
- Nest
- Pack
- Family
- IndividualAI
- explicit plant organisms
- disease agents
- civilization agents

If later gameplay requires them, they must be added because they create new player-facing dynamics, not because biological realism sounds attractive.

---

## 13. Next design dependency

This model depends on the concrete spatial/time rules in:

- `docs/design/WORLD_TIME_SCALE.md`

After both documents are reviewed, the next M0 deliverable is the minimum ecology model and formulas.
