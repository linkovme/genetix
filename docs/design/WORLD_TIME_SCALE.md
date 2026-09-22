# World & Time Scale v0.1

Status: **Draft for product/technical review**

This document defines the first concrete spatial and temporal model for Genetix.

## 1. World representation decision

The first simulation uses a **regular logical square grid**.

Initial reference size:

> **64 × 64 = 4,096 logical cells**

This is a configuration default, not a hardcoded engine assumption.

The simulation API must support other dimensions for testing and future content.

Why a grid:

- simple and deterministic neighborhood logic;
- cheap procedural generation;
- easy climate/resource fields;
- efficient contiguous storage;
- natural chunk partitioning for future parallel work;
- easy map overlays;
- sufficient ecological geography without navmesh/pathfinding complexity.

A graph or irregular region model is not selected for the first prototype because it adds tooling and topology complexity before the core ecology has been validated.

---

## 2. Topology

Reference topology:

- east/west wraps;
- north/south does not wrap;
- four cardinal neighbors are the base adjacency;
- land/water and terrain determine whether migration across an edge is allowed/costly.

East/west wrapping prevents an artificial vertical world boundary and supports a planet-map presentation.

North/south remain bounded so latitude can matter.

Diagonal travel occurs through multiple cardinal steps. This keeps migration and deterministic flux resolution simple.

The renderer may visually smooth terrain and movement; logical adjacency remains grid-based.

---

## 3. What one cell means

A cell is an **abstract ecological region**, not a fixed number of meters or kilometers.

The first prototype intentionally avoids assigning real-world physical dimensions because the simulation compresses ecological and evolutionary time.

A cell is large enough to hold:

- a local resource pool;
- multiple species demes;
- one aggregate climate state;
- local competition/predation;
- migration connections to neighbors.

This abstraction lets the game display continent-scale history without pretending to simulate real animal movement minute by minute.

---

## 4. Minimum environmental fields

Authoritative per-cell state for the first prototype:

### Mostly structural

- elevation;
- land/water;
- latitude index/normalized latitude.

### Dynamic

- temperature;
- moisture;
- fertility/productivity;
- producer biomass;
- disturbance intensity/state.

### Derived

- biome label;
- climate zone;
- habitat suitability for a particular trait profile;
- edge movement cost;
- carrying-capacity estimate.

Biome is a consequence of values, not the authoritative source of those values.

---

## 5. Geography generation

World generation should create large coherent structures, not per-cell noise.

Required large-scale ingredients:

- continents/large landmasses or island-heavy worlds;
- elevation fields;
- mountain barriers;
- wet/dry gradients;
- temperature gradients;
- several separated productive zones;
- migration corridors and bottlenecks.

The generator should deliberately produce **latent tensions** identified in Session Arc v0.2:

- climate edges;
- possible corridors;
- isolation opportunities;
- crowded/productive basins;
- contrasting ecological regions.

Different seeds should therefore alter the strategic structure of the world, not merely texture values.

---

## 6. Initial land/water scope

Water exists geographically in the first world model.

For the first land-ecology prototype:

- land species cannot occupy ocean cells;
- ocean cells can separate land populations;
- narrow land bridges/corridors can matter;
- aquatic ecology is deferred.

This gives islands and continental isolation evolutionary meaning without requiring a full ocean food web.

---

## 7. Execution chunks

The grid is partitionable into rectangular chunks for processing.

Reference implementation target:

> **8 × 8 cells per chunk**

This value has no gameplay meaning and may change after profiling.

Chunks exist to support:

- cache-friendly iteration;
- dirty/active-region processing;
- future worker jobs;
- debug/profiling;
- scalable world sizes.

Cross-chunk operations use the same staged flux/delta rules as cross-cell operations.

---

## 8. Base simulation time

The simulation uses a fixed authoritative timestep.

Initial design value:

> **1 base tick = 100 simulated years**

This is an abstraction chosen for a macro-evolution game, not a claim that ecological events literally happen only once per century.

All formulas should receive/use `dtYears` rather than silently assuming the value 100, so the timestep can be changed during validation if necessary.

Why a fixed step:

- determinism;
- reproducible bugs;
- stable save/replay behavior;
- predictable batch simulation;
- simple speed controls;
- easier future multithreading.

No ecological formula may depend on render frame rate.

---

## 9. Process cadences

Not every expensive analysis must run every base tick.

Initial cadence model:

| Process | Initial cadence |
| --- | --- |
| Environmental modifiers | every tick |
| Resource regeneration | every tick |
| Feeding/predation | every tick |
| Birth/death population update | every tick |
| Migration | every tick |
| Gene-flow bookkeeping | every tick |
| Trait selection/mutation aggregation | every 5 ticks (500 years) |
| Divergence/speciation evaluation | every 10 ticks (1,000 years) |
| Thread detection | every 5–10 ticks depending detector |
| Timeline aggregation | event-driven |

These are scheduler values, not separate simulation clocks.

A speciation check every 1,000 years does **not** mean speciation can happen after 1,000 years. The speciation rule will require accumulated isolation/divergence over much longer simulated time.

---

## 10. Real-time speed controls

Presentation speed controls change how many fixed ticks are executed per real second.

Working UI:

- Pause
- 1×
- 4×
- 16×

Initial implementation hypothesis:

- 1× ≈ 1 base tick/real second;
- 4× ≈ 4 ticks/real second;
- 16× ≈ 16 ticks/real second.

These rates are tuning values, not simulation semantics.

At 16×, one real minute would advance roughly:

> 96,000 simulated years

A player will mix speeds, pause, inspect, and intervene, so session pacing cannot be inferred from a single constant conversion.

Headless tests ignore presentation speed and run ticks as fast as hardware allows.

---

## 11. Evolutionary timescale target

The first serious session should expose meaningful adaptation quickly enough to validate the fantasy.

Initial pacing target:

- visible local adaptation within hundreds of thousands of simulated years;
- plausible first strong divergence within roughly 0.5–2 million simulated years;
- first speciation often within roughly 1–5 million simulated years, depending on isolation and selection pressure.

These are game-scale targets, not biological-realism claims.

They will be tuned against the 60–90 minute Session Arc.

No hidden rule may force speciation because a real-time minute threshold was reached.

---

## 12. Climate timescale

The first prototype supports both:

### Stable baseline climate

A world may begin with a mostly stable global climate while local geography creates variation.

### Slow macro trend

A seed may contain a weak long-term warming or cooling tendency.

The trend should be slow enough for populations to respond and adapt, but strong enough to reshape habitats over a session.

Player climate interventions create explicit EnvironmentModifiers layered onto baseline conditions.

---

## 13. Geological time

Large geological player interventions should not instantly teleport terrain from state A to state B.

Example:

> mountain uplift scheduled over 50,000–200,000 simulated years.

During transition:

- movement cost changes gradually;
- rainfall/climate effects may change gradually;
- gene flow may reduce before complete isolation.

This creates visible causal history rather than a binary edit.

The first prototype does not require continuous plate tectonics.

---

## 14. Deterministic random streams

A world begins from one root seed.

Subsystem randomness should use controlled deterministic streams derived from that seed.

Candidate streams:

- world generation;
- initial biology;
- mutation;
- stochastic demographic variation;
- procedural disturbances.

The purpose is to reduce accidental chaos in debugging.

A future change in cosmetic naming should not consume the same RNG stream as mutation and completely rewrite biological history.

Exact PRNG implementation will be selected with the programming language.

---

## 15. Stage barriers and multithreading path

The first correct implementation should be deterministic even if it runs on one thread.

Future parallel execution can partition work by chunk/species because systems follow this pattern:

1. read stable stage input;
2. compute local outputs/deltas;
3. collect outputs;
4. deterministically reduce them in stable key order;
5. commit at a barrier.

This avoids "whoever thread finished first changed the ecology" behavior.

Parallelism is therefore an execution detail, not a different simulation model.

---

## 16. World-size scalability rule

No gameplay system may assume exactly 64×64.

World dimensions are configuration.

We will benchmark at minimum:

- small test world: 16×16;
- reference world: 64×64;
- stress world: at least 128×128.

The stress size is a technical test target, not a promised player option.

The performance objective is driven by occupied demes and active interactions, not only cell count.

---

## 17. Why not a continuous world

A continuous coordinate simulation would require substantially more work for:

- spatial indexing;
- collision/query structures;
- migration/pathing;
- deterministic neighborhood resolution;
- save precision;
- visualization synchronization.

Those costs do not currently create more of the desired game fantasy.

A grid preserves the important decisions:

- isolation;
- corridors;
- climate gradients;
- range movement;
- refuges;
- barriers;
- island evolution.

Therefore continuous geography is deferred unless playtesting exposes a clear limitation.

---

## 18. Questions intentionally deferred to ecology design

This document does not yet decide:

- exact carrying-capacity formula;
- exact producer regeneration;
- how predators choose prey;
- how competition is normalized;
- migration probability/flux formula;
- trait-selection equation;
- mutation distribution;
- speciation threshold;
- extinction grace rules.

Those belong to the next M0 design document and will operate inside the space/time model defined here.
