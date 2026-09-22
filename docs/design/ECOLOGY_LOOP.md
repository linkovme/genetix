# Ecology Loop v0.1

Status: **Draft for product/technical review**

This document defines the first concrete ecological update model for Genetix.

The goal is not biological realism. The goal is a small deterministic system that can create:

- population growth and decline;
- resource depletion and recovery;
- inter-species competition;
- predator/prey feedback;
- migration;
- colonization failure and success;
- local extinction;
- explainable ecological stories.

The model must remain stable enough for large headless runs and simple enough that important outcomes can be explained to the player.

---

## 1. First-prototype trophic scope

The first ecology prototype contains three functional layers:

1. **Producer biomass**
   - stored per WorldCell;
   - represents edible plant/resource biomass;
   - not yet explicit plant species.

2. **Herbivore species**
   - consume producer biomass;
   - compete with other herbivores in the same cell;
   - can migrate and evolve.

3. **Predator species**
   - consume prey biomass from eligible consumer species;
   - compete with other predators for prey;
   - can migrate and evolve.

Deferred:

- omnivory;
- detritus;
- scavenging;
- parasites;
- diseases;
- explicit plant species;
- aquatic food webs;
- multi-resource nutrition.

This limited scope is sufficient to validate the autonomous ecological loop.

---

## 2. Units and normalization

The first implementation should avoid fake real-world units where they do not improve gameplay.

### Population

`abundance` is an aggregate expected count of individuals.

Internally it may be floating-point to keep smooth deterministic dynamics.

Player-facing population can be rounded/formatted.

### Biomass

Producer and prey food availability use abstract **biomass units**.

A species' body-size trait converts population abundance into edible biomass.

### Time

All rate calculations receive:

`dtYears`

Initial reference:

`dtYears = 100`

Do not hide this value inside formulas.

### Normalized factors

Many ecological factors should resolve to values in:

`[0, 1]`

Examples:

- habitat suitability;
- food satisfaction;
- attack success;
- movement suitability;
- disturbance survival factor.

This improves tuning and explanation.

---

## 3. Per-tick ecology pipeline

For one authoritative base tick:

1. update environment modifiers;
2. derive habitat/productivity values;
3. regenerate producer biomass;
4. calculate herbivore food demand;
5. allocate producer consumption;
6. calculate predator demand and prey availability;
7. resolve predation;
8. calculate non-predation population growth/decline;
9. calculate migration;
10. apply population deltas;
11. create/remove demes;
12. evaluate local/species extinction;
13. record pressure summaries and notable events;
14. pass stable post-ecology state to evolutionary systems.

Important:

> No system may rely on mutations made halfway through another system's iteration order.

Demand, fluxes, kills and migrations are calculated first, then deterministically reduced and committed.

---

# 4. Habitat suitability

Each consumer deme receives a habitat suitability score:

`H ∈ [0,1]`

Initial climate inputs:

- temperature;
- moisture.

A trait profile contains:

- preferred temperature;
- temperature tolerance;
- preferred moisture;
- moisture tolerance.

For one environmental dimension:

`match = exp(-0.5 * (difference / tolerance)^2)`

Then:

`H = temperatureMatch * moistureMatch * disturbanceFactor`

This produces:

- near-optimal climate → score near 1;
- moderate mismatch → gradual penalty;
- severe mismatch → near 0.

Tolerance is clamped to a configured minimum to avoid division-by-zero or infinitely narrow niches.

### Why a smooth function

A hard rule such as:

> temperature > 20 → dies

would make worlds brittle and produce abrupt unexplained borders.

Smooth suitability creates gradual range edges, refuges and adaptation pressure.

---

# 5. Producer biomass

Each land cell stores:

- `producerBiomass`
- `producerCapacity`

The capacity is derived from:

- fertility/productivity;
- moisture;
- temperature;
- disturbance modifiers.

## 5.1 Producer capacity

Initial conceptual form:

`Kproducer = baseCapacity * fertilityFactor * climateProductivity`

All factors are configurable.

The exact productivity curve will be tuned experimentally.

## 5.2 Stable regeneration

Instead of naïve Euler logistic growth, use exponential relaxation toward current capacity:

`Bregen = K - (K - Bcurrent) * exp(-regenRate * dtScale)`

where:

`dtScale = dtYears / referenceYears`

Then clamp:

`Bregen ∈ [0, K]`

Advantages:

- numerically stable for larger fixed ticks;
- biomass can recover from zero;
- changing climate automatically changes the target;
- no overshoot above capacity.

The ecology stage then allows consumers to remove biomass from `Bregen`.

---

# 6. Herbivore food demand and competition

For each herbivore deme:

`foodDemand = abundance * foodNeedPerIndividual`

`foodNeedPerIndividual` is derived from species/deme traits, primarily:

- body size;
- metabolic demand.

Exact allometric scaling is not required in v0.1.

A simple tunable function is preferred.

## 6.1 Foraging claim

Each herbivore produces:

`claim = foodDemand * foragingEfficiency * habitatActivity`

where:

- `foragingEfficiency` derives from traits;
- `habitatActivity` is a bounded function of habitat suitability.

## 6.2 Shared allocation

All herbivore demes in a cell compete for the same producer biomass.

If available biomass satisfies all demands:

- each deme receives its demand.

If biomass is scarce:

- biomass is allocated using deterministic capped proportional allocation based on claims;
- no deme can receive more than its demand;
- unused remainder is redistributed until either all biomass is assigned or all demands are satisfied.

This avoids:

- processing-order advantage;
- one species consuming food simply because it was iterated first;
- biomass creation.

For each deme:

`foodSatisfaction = actualConsumption / foodDemand`

clamped to:

`[0,1]`

If demand is effectively zero, satisfaction is defined as 1.

## 6.3 Producer commit

After allocation:

`producerBiomassNext = Bregen - totalConsumed`

Invariant:

`producerBiomassNext >= 0`

---

# 7. Predator-prey model

Predators consume animal biomass rather than directly comparing arbitrary population counts.

For each prey deme:

`availablePreyBiomass = abundance * bodyBiomass`

Not every prey species is equally catchable.

## 7.1 Attack compatibility

For predator p and prey h:

`attackScore(p,h) ∈ [0,1]`

Initial inputs may include:

- predator attack/foraging efficiency;
- prey defense/evasion;
- predator/prey body-size ratio;
- diet/prey preference.

The exact function belongs to tuning data, not species-specific code.

## 7.2 Functional response

Predator demand should not imply that doubling prey indefinitely doubles kill rate.

Use a saturating functional response:

`availabilityResponse = preyBiomass / (preyBiomass + halfSaturation)`

Then a predator-prey requested consumption amount is based on:

`predatorFoodDemand * attackScore * availabilityResponse * preyPreferenceShare`

## 7.3 Prey safety cap

For numerical stability, a single ecology tick may not remove an unlimited fraction of a prey deme.

Initial configurable bound:

`maxPredationFractionPerTick`

Example starting hypothesis:

`0.35`

This is a stabilization parameter, not a permanent biological truth.

If all predators request more prey biomass than the allowed kill budget:

- kills are allocated proportionally by predator request;
- no prey biomass is consumed twice.

## 7.4 Predator food satisfaction

For each predator:

`predatorFoodSatisfaction = actualConsumedPreyBiomass / predatorFoodDemand`

clamped to `[0,1]`.

This drives future predator population growth.

## 7.5 Prey mortality

Consumed prey biomass converts back to abundance killed using the prey's body-biomass value.

Predation deaths are therefore a measured population delta and can be shown directly in causality UI.

---

# 8. Non-predation population dynamics

The first prototype uses a bounded net-growth model.

For each consumer deme define:

- `F` = food satisfaction in [0,1];
- `H` = habitat suitability in [0,1];
- `D` = disturbance survival factor in [0,1].

## 8.1 Reproductive condition

Initial form:

`reproductiveCondition = F * H * D`

## 8.2 Stress

Initial forms:

`foodStress = (1 - F)^stressExponent`

`climateStress = (1 - H)^stressExponent`

`disturbanceStress = 1 - D`

## 8.3 Net non-predation rate

Conceptual equation:

`rNet = maxGrowthRate * reproductiveCondition`
`       - backgroundMortality`
`       - starvationMortality * foodStress`
`       - climateMortality * climateStress`
`       - disturbanceMortality * disturbanceStress`

All rate coefficients are data-driven.

Then:

`growthMultiplier = exp(clamp(rNet * dtScale, minLogGrowth, maxLogGrowth))`

`populationBeforePredationAndMigration = abundance * growthMultiplier`

This exponential form:

- keeps population non-negative;
- behaves predictably across different dt;
- avoids Euler-step explosions.

Predation kills are then subtracted as an explicit measured delta.

## 8.4 Growth caps

The prototype should still impose configurable per-tick maximum growth and decline multipliers as numerical safety rails.

These are not intended to create the ecological equilibrium.

Food competition and habitat suitability should do that.

The caps protect the simulation from bad parameter combinations during tuning.

---

# 9. Migration

Migration is not a random teleport.

A deme compares its local condition with neighboring reachable cells.

## 9.1 Emigration pressure

Initial factors:

- low food satisfaction;
- poor habitat suitability;
- local crowding/competition;
- baseline dispersal tendency;
- disturbance pressure.

Conceptual:

`emigrationFraction = clamp(baseDispersal + stressDrivenDispersal, 0, maxMigrationFraction)`

A healthy population may still send a small dispersing fraction.

A stressed population sends more if viable destinations exist.

## 9.2 Destination attractiveness

For each reachable neighbor:

`destinationScore = habitatSuitability`
`                 * expectedFoodOpportunity`
`                 * movementAccessibility`
`                 * crowdingOpportunity`

All components normalize to `[0,1]`.

Only destinations better than a configurable threshold receive meaningful flow.

## 9.3 Flux allocation

The emigration budget is distributed across candidate neighbors in proportion to destination scores.

Migration is computed as fluxes:

`(SpeciesId, sourceCell, destinationCell, abundance, traitPayload)`

All fluxes are committed after calculation.

## 9.4 Trait flow

Migrants carry their source deme's trait statistics.

When immigrants join an existing deme:

- abundance combines;
- local trait mean/variance is merged by population-weighted statistics.

This is how migration creates gene flow without simulating individuals.

## 9.5 Colonization

If migrants enter an empty viable cell, a new deme may be created.

Initial requirements:

- destination is traversable;
- migrant abundance exceeds a founder threshold;
- local habitat is above a minimum viability threshold.

Founder effects are part of the evolution model and can initially be represented through trait sampling/variance adjustment rather than individuals.

---

# 10. Extinction rules

Fractional aggregate populations must not create immortal ghost species.

## 10.1 Local deme extinction

A deme is removed when:

- abundance reaches zero; or
- abundance remains below `minimumViableAbundance` for `extinctionGraceTicks`.

Initial hypothesis:

- small grace period of a few ticks.

This avoids a deme flickering in/out from tiny numerical changes.

## 10.2 Species extinction

A Species becomes extinct when:

- it has no living demes after all ecology/migration commits.

The species remains in:

- LineageGraph;
- WorldHistory;
- tree-of-life UI.

Extinction is irreversible in the first design.

---

# 11. Ecological causality ledger

Genetix must explain outcomes using the actual simulation mathematics.

Each living deme keeps or derives a recent **PressureSummary**.

Candidate fields:

- food satisfaction;
- climate suitability;
- predation mortality fraction;
- competition level;
- disturbance pressure;
- migration inflow/outflow;
- recent net growth;
- main limiting factor.

For an observed decline the UI may say:

> Population −18% over the recent period
>
> Strongest measured pressures:
> 1. Food shortage
> 2. Predation
> 3. Heat mismatch

These rankings come from recorded mathematical contributions.

The game must not invent narrative explanations unrelated to simulation state.

Pressure values may use exponential moving averages so the UI reflects trends rather than one noisy tick.

---

# 12. Thread detectors enabled by ecology

The first ecology model should support at least these systemic Threads.

### Resource collapse

Triggered when:

- producer biomass is falling rapidly;
- consumer demand materially exceeds regeneration;
- affected population is significant.

### Migration front

Triggered when:

- a species is consistently colonizing a new region/edge.

### Refuge

Triggered when:

- a large fraction of a declining species becomes concentrated in a small set of suitable cells.

### Predator-prey instability

Triggered when:

- prey decline and predator dependency imply high collapse risk.

### Competition displacement

Triggered when:

- one consumer lineage is gaining share while another loses abundance in overlapping cells, with food competition a strong measured pressure.

Threads observe the ecology model; they never modify its calculations.

---

# 13. Determinism and stochasticity

Ecology v0.1 should be **mostly deterministic** after world generation.

Do not initially add random demographic noise to every population tick.

Reasons:

- easier debugging;
- easier formula tuning;
- easier reproduction of balance failures;
- world seeds and evolving traits already generate variety.

Controlled stochastic effects may later be added where they produce visible gameplay value, particularly:

- tiny founder populations;
- severe bottlenecks;
- rare disturbances.

All such randomness must use controlled subsystem RNG streams.

---

# 14. Numerical invariants

Every tick should validate, at least in tests/debug runs:

### Resource conservation

- producer biomass never negative;
- herbivores cannot consume more producer biomass than available.

### Predation budget

- total prey deaths from predation cannot exceed the configured killable prey budget;
- the same prey biomass cannot feed multiple predators.

### Migration conservation

Ignoring births/deaths/predation:

`sum population before migration == sum population after migration`

within floating-point tolerance.

### Population safety

- no NaN;
- no Infinity;
- no negative abundance;
- every live deme has finite valid trait values.

### Deterministic reduction

Flux/result ordering is stable by IDs before commit.

---

# 15. Data-driven tuning parameters

No ecology coefficient should be scattered as a magic number in algorithms.

Initial parameter groups:

### Producer

- base capacity;
- regeneration rate;
- climate productivity curves.

### Consumer

- food-need scaling;
- base growth;
- background mortality;
- starvation mortality;
- climate mortality;
- disturbance mortality.

### Predation

- attack curve;
- size-match curve;
- half-saturation;
- maximum predation fraction.

### Migration

- baseline dispersal;
- stress-driven dispersal;
- maximum migration fraction;
- destination threshold;
- movement-cost weights;
- founder threshold.

### Extinction

- minimum viable abundance;
- extinction grace ticks.

Parameter sets should be serializable/versioned so batch runs can compare balance configurations.

---

# 16. Required micro-scenarios

Before testing a full procedural world, ecology must pass controlled scenarios.

## Scenario A — producer recovery

A cell with low biomass and no consumers approaches its environmental carrying capacity without overshoot/explosion.

## Scenario B — one herbivore

One herbivore with abundant resources grows, increases consumption, then settles into bounded dynamics rather than growing forever.

## Scenario C — starvation

An oversized herbivore population depletes food and subsequently declines.

## Scenario D — competition

Two herbivores sharing one limiting resource affect one another through real resource allocation.

No iteration-order advantage.

## Scenario E — predator introduction

A predator introduced into abundant prey can grow, but predation is capped and cannot delete the prey instantly.

## Scenario F — predator overshoot

If predators become too abundant, prey declines; food satisfaction for predators then falls; predator abundance subsequently declines.

## Scenario G — climate mismatch

A population moved into a highly unsuitable climate declines even if food is abundant.

## Scenario H — migration

A stressed population with an attractive neighboring habitat generates conservative migration flux and can colonize it.

## Scenario I — barrier

Removing accessibility between neighboring regions stops direct gene/population flow without changing unrelated ecology.

## Scenario J — local extinction

A chronically nonviable tiny deme disappears cleanly and does not survive forever as fractional numerical dust.

---

# 17. Headless ecology metrics

Batch runs should output at minimum:

### World health

- living species count;
- living deme count;
- total consumer abundance;
- occupied land fraction;
- producer biomass fraction of capacity.

### Stability

- time to total consumer extinction;
- fraction of worlds retaining consumers after target epochs;
- frequency of global runaway growth;
- frequency of permanent static equilibrium.

### Dynamics

- number of local extinction events;
- number of colonizations;
- migration distance/turnover;
- population coefficient of variation;
- predator/prey oscillation indicators;
- resource-collapse frequency.

### Diversity of outcomes

Across seeds:

- variance in occupied area;
- variance in surviving species;
- variance in trophic composition;
- variance in dominant pressures.

The goal is not to maximize every metric.

We need a region of parameter space where worlds are alive, variable, sometimes unstable, but not routinely broken.

---

# 18. Prototype acceptance criteria

Ecology v0.1 is promising if:

1. stable producer-only worlds remain numerically stable;
2. consumer populations can self-limit through resources;
3. multiple consumers genuinely compete;
4. predator/prey feedback can create recovery/decline cycles;
5. migration produces changing ranges;
6. local extinction occurs for understandable reasons;
7. most baseline worlds do not rapidly end in universal extinction;
8. most baseline worlds also do not become perfectly static;
9. measured pressure summaries correctly explain major changes;
10. all systems can run headlessly much faster than real time.

---

# 19. Important intentional simplifications

The first ecology prototype knowingly ignores:

- age structure;
- sex ratios;
- individual territories;
- pack behavior;
- explicit reproduction seasons;
- nutrient cycles;
- plant species;
- carcasses;
- scavengers;
- disease;
- detailed prey handling time;
- real-world metabolic units.

These can be added later only if they produce valuable gameplay that cannot be achieved with the simpler model.

---

# 20. Next dependency

After Ecology Loop v0.1 is accepted, M0 should define:

> **Evolution & Speciation v0.1**

That document must specify:

- mutation/trait variation;
- selection from ecology pressures;
- trait mixing via migration;
- population clustering/gene flow;
- divergence accumulation;
- speciation trigger;
- lineage split behavior;
- founder effects/bottlenecks;
- evolutionary causality shown to the player.
