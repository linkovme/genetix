# Evolution & Speciation v0.1

Status: **Draft for product/technical review**

This document defines the first concrete evolutionary model for Genetix.

The model must create believable game-scale adaptation, divergence and speciation without simulating:

- individual organisms;
- chromosomes;
- alleles;
- mating pairs;
- literal DNA sequences.

The authoritative evolutionary state lives at the **deme** level and operates on aggregate trait statistics.

---

# 1. Product promise

Evolution in Genetix must feel like:

> **Ecology creates pressure. Populations respond gradually. Isolation allows responses to diverge. Gene flow resists divergence. New species emerge only after a long visible process.**

The player must be able to inspect a new species and understand the causal chain that produced it.

Speciation must never behave like:

> random chance → "New species!" popup.

---

# 2. Trait state

Each living deme owns an evolving trait distribution summarized by:

- `traitMean[]`
- `traitVariance[]`

The first prototype does not store full distributions or individual genotypes.

A trait therefore has:

- current local mean;
- amount of heritable variation available for selection.

Example:

`thermalOptimum.mean = 0.42`

`thermalOptimum.variance = 0.018`

Two demes of the same Species may have different local means and variances.

---

# 3. Initial trait set

Exact tuning is not locked, but the first candidate vector is:

1. body size;
2. dispersal tendency;
3. reproduction tendency;
4. metabolic demand;
5. thermal optimum;
6. thermal tolerance;
7. moisture optimum;
8. moisture tolerance;
9. foraging / attack efficiency;
10. defense / evasion.

Feeding guild remains initially constrained at species level:

- herbivore;
- predator.

The first prototype does **not** allow a herbivore to mutate into a predator through a continuous trait.

Major trophic transitions are deferred until the core model is proven.

All traits use normalized internal ranges where practical.

---

# 4. Evolution cadence

Evolution does not need to update every ecological tick.

Reference cadence from `WORLD_TIME_SCALE.md`:

> trait evolution update every 5 base ticks = 500 simulated years

All equations receive the actual elapsed evolutionary time.

This is a tuning cadence, not a biological claim.

---

# 5. Fitness proxy

The system needs a consistent answer to:

> Would this deme perform better or worse if one inherited trait were slightly different?

Define a local **fitness proxy**:

`W(deme, environment, ecologyContext, traits)`

It is not intended to represent literal lifetime reproductive success.

It is a normalized game-scale estimate based on the same pressures that drive ecology.

Candidate inputs:

- expected food satisfaction;
- habitat suitability;
- expected predation mortality;
- expected metabolic cost;
- reproduction potential;
- local movement / dispersal value where relevant.

Conceptually:

`W = survivalComponent * reproductionComponent`

with bounded numerical ranges.

The critical architecture rule is:

> The fitness evaluator reuses ecological rules and measured local context instead of inventing an unrelated evolution-only scoring system.

---

# 6. Generic selection gradient

Rather than manually coding adaptation directions for every possible pressure, each evolvable trait uses a local finite-difference estimate.

For trait `i`:

`Wminus = Fitness(trait_i - epsilon_i)`

`Wplus = Fitness(trait_i + epsilon_i)`

Then:

`selectionGradient_i = (Wplus - Wminus) / (2 * epsilon_i)`

This asks:

> In the current environment, would a slightly higher or lower value of this trait be favored?

Examples emerge automatically:

- colder climate can favor lower thermal optimum and/or broader cold tolerance;
- scarce food can favor lower metabolic demand;
- abundant food may allow higher reproduction;
- strong predation can favor defense/evasion;
- difficult prey can favor attack efficiency;
- fragmented landscapes may favor dispersal under some contexts;
- stable isolated habitats may reduce the value of dispersal.

No special-case story rule is required for each situation.

---

# 7. Mean trait evolution

For each trait:

`deltaMean_i = evolvability_i * variance_i * selectionGradient_i * dtEvolution`

Then apply:

- trait-specific maximum evolutionary step;
- valid trait bounds;
- tradeoff constraints;
- deterministic numerical ordering.

The local mean becomes:

`meanNext_i = clamp(mean_i + deltaMean_i)`

This is inspired by quantitative-genetic response-to-selection ideas, but intentionally simplified for game-scale simulation.

Important:

> Strong selection cannot create adaptation when a deme has zero heritable variance.

Variation matters.

---

# 8. Mutation and maintenance of variation

Without mutation, variance would eventually disappear and evolution would stop forever.

Each trait has a configured mutation/variation input.

Conceptual variance update:

`variance += mutationInput * dtEvolution`

Variance is bounded by:

- minimum mutation-supported floor;
- trait-specific maximum.

Mutation primarily replenishes variation.

It should **not** randomly teleport the trait mean every update.

This keeps evolution gradual and explainable.

---

# 9. Variance loss and bottlenecks

Small populations should lose evolutionary diversity faster.

Define a deterministic diversity-retention factor based on effective local population size.

Conceptually:

`retention = population / (population + driftScale)`

Then variance trends downward faster when population is small.

This creates:

- bottleneck effects;
- reduced adaptability in tiny refuges;
- evolutionary consequences of near-extinction.

Ecology remains deterministic, and this first variance-loss model may also remain deterministic.

Controlled stochastic drift can be added later if it creates better stories.

---

# 10. Founder effect

Colonization by a small migrant group may create a new deme whose trait mean differs slightly from the source.

This is one of the few places where controlled stochasticity is valuable in v0.1.

When a new deme is founded:

- migrants carry source trait mean and variance;
- founder size determines sampling strength;
- a deterministic RNG stream samples a small mean offset from the source distribution;
- smaller founder populations produce larger possible offsets;
- resulting variance may be reduced by the founder bottleneck.

All randomness comes from the evolution/founder RNG stream.

Given the same seed and command history, the founder effect is reproducible.

---

# 11. Gene flow

Migration is the mechanism of gene flow.

When migrants join an existing deme, trait statistics merge using population-weighted moments.

For populations A and B:

`meanCombined = (nA * meanA + nB * meanB) / (nA + nB)`

Variance must use the correct pooled-variance calculation so that differences between means contribute to combined variance.

Therefore migration can:

- pull locally adapted means toward one another;
- replenish variance;
- erase weak divergence;
- prevent speciation.

This is central to Genetix:

> Geography matters because geography changes gene flow.

---

# 12. Tradeoffs

Traits cannot all evolve upward without cost.

The first model must include explicit data-driven tradeoffs.

Candidate examples:

### Body size

Benefits may include:

- defense;
- predator/prey compatibility;
- environmental resilience.

Costs:

- higher food/metabolic demand;
- slower reproduction.

### Reproduction tendency

Benefit:

- faster population recovery/growth.

Costs:

- increased metabolic/resource requirement;
- reduced investment in survival/defense.

### Broad climate tolerance

Benefit:

- viable across more environments.

Cost:

- lower peak efficiency in ideal conditions.

### Dispersal

Benefit:

- colonization and escape from poor habitat.

Cost:

- energetic/reproductive efficiency penalty or reduced local specialization.

Tradeoffs should be implemented through shared parameterized functions, not species-specific exceptions.

---

# 13. Evolution pressure explanation

Each evolution update should produce a small **SelectionSummary** for important traits.

Example:

> Cold tolerance: ↑
>
> Main measured reasons:
> - current temperature below local optimum;
> - cold-tolerant counterfactual has higher expected survival.
>
> Body size: ↓
>
> Main measured reasons:
> - food satisfaction low;
> - smaller-bodied counterfactual has lower metabolic cost.

The game does not need to display derivatives or equations.

It displays the direction and dominant pressures.

This allows the player to understand adaptation before speciation occurs.

---

# 14. From demes to evolutionary groups

A Species may occupy many cells.

Speciation analysis must determine whether its demes still behave like one interconnected evolutionary population.

Build a same-species **gene-flow graph**:

- node = living deme;
- edge = recent effective migration/gene flow between neighboring demes;
- edge strength normalized by the sizes of the connected populations.

Very weak edges are ignored for evolutionary connectivity.

The graph is recalculated on the speciation cadence.

This produces one or more **evolutionary groups** within a Species.

A group may span many adjacent cells.

---

# 15. Gene-flow separation

For each pair of candidate evolutionary groups, calculate a normalized:

`geneFlowIsolation ∈ [0,1]`

Interpretation:

- 0 = strongly connected;
- 1 = effectively isolated.

Isolation depends on recent sustained effective migration, not merely physical distance.

Therefore:

- two neighboring populations separated by an impassable mountain may be strongly isolated;
- distant populations connected by a corridor may remain genetically connected.

---

# 16. Trait divergence

For two evolutionary groups A and B, calculate normalized trait distance.

For each speciation-relevant trait:

`z_i = abs(meanA_i - meanB_i) / divergenceScale_i`

Then use a weighted RMS or equivalent bounded aggregation:

`traitDistance = sqrt(sum(weight_i * z_i^2) / sum(weight_i))`

Clamp/transform to a convenient bounded score for UI.

Species-wide group means are abundance-weighted from their demes.

Not every trait must have equal speciation weight.

---

# 17. Ecological divergence

Two populations may differ not only genetically but in occupied niches.

Calculate an optional:

`ecologicalDistance ∈ [0,1]`

Candidate inputs:

- mean temperature occupied;
- mean moisture occupied;
- food/prey composition;
- trophic performance context;
- habitat-use distribution.

This is used as supporting evidence, not an independent random speciation trigger.

---

# 18. Persistent divergence candidate

A new species is not created the first time two groups happen to be different.

For a candidate split, `DivergenceState` tracks:

- group identity continuity;
- isolation history;
- trait-distance history;
- ecological-distance history;
- duration;
- minimum population sizes;
- reconnection events.

The candidate accumulates **speciation progress** only while meaningful separation persists.

---

# 19. Speciation progress

Define a normalized instantaneous divergence pressure:

`P = isolationWeight * geneFlowIsolation`
`    + traitWeight * traitDistance`
`    + ecologyWeight * ecologicalDistance`

But progress is gated:

- isolation must exceed a minimum;
- trait distance must exceed a minimum;
- both groups must exceed viability thresholds.

Conceptually:

`progress += progressRate(P) * dtSpeciation`

If gene flow rises again or trait distance collapses:

`progress -= reconnectionDecayRate * dtSpeciation`

clamped to:

`[0, 1]`

Speciation occurs when progress reaches 1.

This creates a visible long-running process rather than a binary threshold flicker.

---

# 20. Speciation event

When a candidate reaches completion:

1. identify the diverging evolutionary group;
2. allocate a new SpeciesId;
3. parent SpeciesId = current species;
4. reassign the group's demes to the new species;
5. preserve their local trait means and variances;
6. create a LineageGraph branch;
7. create a WorldEvent;
8. reset/rebuild relevant divergence candidates;
9. expose a player-facing explanation.

Deterministic rule for identity:

> the largest connected group retains the existing parent SpeciesId; the qualifying separated group receives the new SpeciesId.

Ties resolve by stable CellId/group ordering.

This avoids arbitrary identity changes across deterministic replay.

---

# 21. Multi-way fragmentation

A species can eventually fragment into more than two groups.

The first implementation should resolve speciation events **one qualifying branch at a time** in stable deterministic order.

Reasons:

- simpler lineage history;
- easier explanation;
- lower risk of one evaluation spawning five species simultaneously;
- easier tests.

Remaining isolated groups can continue accumulating divergence afterward.

---

# 22. Reconnection before speciation

If geography changes and two groups reconnect before speciation completes:

- migration resumes;
- trait means begin mixing;
- isolation score falls;
- speciation progress decays.

A Thread may resolve as:

> Divergence reversed — populations reconnected.

This creates important player agency:

> opening a corridor can prevent a split without directly pressing "cancel evolution."

---

# 23. Secondary contact after speciation

Once a new Species is created, the two species are treated as distinct lineages.

In v0.1:

- they do not merge back into one Species automatically;
- later range overlap creates ecological competition/predation interactions according to normal rules;
- explicit hybridization is deferred.

This keeps species identity and the tree of life stable.

---

# 24. Extinction and evolution

Evolution cannot guarantee rescue.

A population under rapidly worsening conditions may:

- adapt;
- migrate;
- decline faster than adaptation can respond;
- lose variance through bottleneck;
- go extinct.

This race is desirable.

The player should sometimes see:

> "They were adapting toward cold tolerance, but the climate changed faster than the population could respond."

That is a meaningful story, not simulation failure.

---

# 25. Evolutionary rescue Thread

Candidate Thread:

### Adaptation race

Triggered when:

- a major ecological pressure is strong;
- selection on one or more traits is consistently directional;
- population is declining;
- projected trait adaptation is materially changing suitability/survival.

Possible resolutions:

- adaptation stabilizes the population;
- migration removes the pressure;
- environment changes;
- population goes extinct.

Again, the Thread observes the system and does not modify it.

---

# 26. Divergence Thread

### Diverging populations

Triggered when:

- a Species has at least two meaningful evolutionary groups;
- gene flow is low;
- trait divergence is increasing over a sustained period.

Player-facing summary example:

> **Kara minor is splitting**
>
> Northern group:
> - colder habitat;
> - larger body size;
> - higher cold tolerance.
>
> Gene flow: very low
> Divergence trend: increasing

Do not initially show "73% chance of speciation."

A deterministic progress indicator may eventually be shown, but it should communicate state rather than pretend to predict biological destiny.

---

# 27. Speciation causality record

A speciation WorldEvent should retain enough information to reconstruct why it happened.

Store/snapshot:

- parent species;
- child species;
- split time;
- origin cells/region summary;
- isolation duration;
- gene-flow score near split;
- largest diverged traits;
- ecological differences;
- approximate populations at split;
- relevant player intervention references when causal proximity is strong.

The UI may later say:

> **Kara borealis emerged from Kara minor**
>
> Primary drivers:
> - 1.4 million years of low gene flow;
> - colder northern habitat;
> - increased cold tolerance;
> - increased body size.
>
> The mountain uplift 1.7 million years earlier contributed to the isolation.

The final sentence is shown only if intervention/event causality is actually traceable from recorded geography/history.

---

# 28. No mutation loot table

Do not implement mutations as:

> +5% speed
> -3% size
> rare mutation unlocked

for the core evolutionary process.

Mutation supplies variation.

Selection changes population averages.

This distinction is fundamental to the intended fantasy.

Rare discrete innovations may be considered much later for genuinely new capabilities such as:

- flight;
- aquatic transition;
- major diet transition.

Those would be separate macro-evolution systems, not replacements for continuous adaptation.

---

# 29. Numerical safeguards

Evolution update must guarantee:

- finite trait values;
- trait bounds respected;
- non-negative finite variance;
- deterministic update order;
- maximum mean shift per evolutionary step;
- no speciation from groups below minimum viable size;
- no candidate progress from one-tick isolation spikes;
- no new SpeciesId allocation dependent on thread scheduling.

---

# 30. Required micro-scenarios

Before procedural worlds, evolution must pass controlled tests.

## Scenario A — thermal adaptation

Two identical demes experience different stable temperatures.

Expected:

- their thermal traits gradually move in different directions;
- no instant jumps.

## Scenario B — no variation

A deme with near-zero trait variance under strong pressure adapts extremely slowly or not at all until mutation replenishes variation.

## Scenario C — gene-flow resistance

Two environments favor different traits but strong migration connects the demes.

Expected:

- divergence remains limited.

## Scenario D — barrier divergence

The same scenario with migration removed.

Expected:

- local trait means diverge materially.

## Scenario E — reconnection

After prolonged divergence, restore migration before speciation completes.

Expected:

- means begin converging;
- speciation progress declines.

## Scenario F — clean speciation

Maintain strong isolation and divergent selection long enough.

Expected:

- exactly one deterministic lineage split;
- demes reassigned correctly;
- lineage graph updated;
- event explanation matches measured history.

## Scenario G — founder effect

Colonize an empty region with a small group.

Expected:

- reproducible founder offset;
- reduced variance;
- same seed reproduces the exact result.

## Scenario H — bottleneck

Reduce a deme to very low abundance, then recover it.

Expected:

- trait variance is lower after the bottleneck;
- adaptation potential temporarily reduced.

## Scenario I — evolutionary rescue

Apply a pressure slowly enough that adaptation can compensate.

Expected:

- population initially declines or struggles;
- suitable trait moves;
- population stabilizes/rebounds for measurable causal reasons.

## Scenario J — pressure too fast

Apply the same environmental direction much faster.

Expected:

- adaptation begins but extinction can occur before rescue.

---

# 31. Headless evolution metrics

Batch simulations should report:

### Adaptation

- mean trait-change magnitude per lineage;
- fraction of major habitat shifts followed by adaptive trait movement;
- adaptation lag versus environmental change.

### Gene flow

- within-species trait variance across demes;
- divergence under high vs low migration;
- frequency of reconnection-reversed divergence.

### Speciation

- speciation events per million simulated years;
- time from initial isolation to split;
- child population at origin;
- parent/child persistence after split;
- geographic pattern of splits.

### Extinction interaction

- fraction of declining populations showing adaptive response;
- evolutionary rescue frequency;
- extinction during ongoing adaptation;
- variance loss during bottlenecks.

### Diversity

- lineage count over time;
- tree depth;
- branch lifetime distribution;
- fraction of worlds dominated by one hyper-successful lineage.

The target is not maximum speciation.

The target is a readable, varied evolutionary history.

---

# 32. Anti-pathologies

The prototype must detect and flag:

### Speciation explosion

Dozens of species appear from tiny transient separations.

Possible causes:

- thresholds too low;
- progress too fast;
- minimum group size too small.

### No speciation

Strongly isolated groups never split.

Possible causes:

- gene-flow score too sticky;
- trait response too weak;
- threshold too high.

### Trait boundary collapse

Most lineages converge to min/max values.

Possible causes:

- missing tradeoffs;
- fitness proxy rewards one direction globally.

### Infinite generalist

Maximum tolerance dominates every world.

Possible cause:

- no cost to broad tolerance.

### Infinite disperser

Maximum movement always wins.

Possible cause:

- no local cost to dispersal.

### Evolution ignores ecology

Traits move even when pressure does not support the direction.

This is a correctness bug.

---

# 33. Data-driven parameters

Evolution tuning belongs in versioned parameter sets.

Initial groups:

### Selection

- trait epsilon;
- evolvability;
- max step;
- selection sensitivity.

### Variation

- mutation variance input;
- minimum variance;
- maximum variance;
- drift/bottleneck scale.

### Founder effect

- founder sampling strength;
- variance reduction;
- minimum founder size.

### Gene flow

- effective-flow window;
- connectivity threshold;
- edge normalization.

### Divergence

- trait scales;
- trait weights;
- ecological weights;
- minimum isolation;
- minimum trait distance;
- minimum group population;
- progress rate;
- reconnection decay.

### Tradeoffs

- trait cost curves;
- interaction coefficients.

No values in this document should become scattered magic numbers in code.

---

# 34. Performance model

If there are ~6,000 active demes and 10 traits:

A finite-difference selection update may require roughly:

> 6,000 × 10 × 2 = 120,000 lightweight fitness evaluations

per evolution update cadence.

This is a reasonable starting scale for headless profiling, especially because the update does not run every ecology tick.

Optimization options later include:

- skip traits with negligible variance;
- skip stable demes below pressure thresholds;
- analytically derive gradients for simple functions;
- vectorize/data-orient evaluation;
- parallelize independent deme evaluation;
- cache environmental components.

Correctness and clarity come first.

---

# 35. Prototype acceptance criteria

Evolution & Speciation v0.1 is promising if:

1. populations adapt directionally to real ecological pressure;
2. trait change is gradual and explainable;
3. genetic variation controls evolutionary responsiveness;
4. migration measurably resists divergence;
5. barriers measurably enable divergence;
6. reconnection can reverse incomplete divergence;
7. sustained isolation + divergent selection can produce speciation;
8. speciation is neither constant nor impossibly rare across baseline seeds;
9. founder/bottleneck effects are reproducible and visible;
10. adaptation can sometimes rescue a lineage and sometimes fail;
11. lineage history remains deterministic under identical seeds/commands;
12. important evolutionary changes can be explained from stored pressure/history data.

---

# 36. Explicitly deferred

Not required for the first evolutionary prototype:

- sexual selection;
- sex chromosomes;
- assortative mating;
- hybridization;
- explicit reproductive incompatibility genes;
- polyploidy;
- neutral molecular clocks;
- genetic recombination;
- individual pedigrees;
- learned behavior;
- cultural evolution;
- discrete body-part evolution;
- flight;
- intelligence;
- trophic-guild transitions.

These may later become separate systems if the game proves they are worth the added complexity.

---

# 37. Next M0 dependency

After this document is accepted, the next design block is:

> **Player Influence & Intervention Economy v0.1**

It must define:

- what the player can change;
- spatial/temporal scale of interventions;
- Influence generation and cap;
- why maximum-speed farming is not optimal;
- intervention costs;
- cooldown/consequence windows if needed;
- how interventions enter deterministic replay;
- how the game attributes later consequences without falsely claiming one perfect cause.
