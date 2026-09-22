# Session Arc v0.1

Status: **Draft v0.2 for product review**

This document defines the first target play session for Genetix. It is intentionally concrete enough to drive simulation design, but values and timings remain hypotheses until prototype testing.

## 1. Session promise

A first serious session should last roughly **60–90 minutes without requiring handcrafted levels**.

By the end of that time, the player should be able to look at the planet and say something like:

> "That mountain range split one population. One branch adapted to the colder side, the other followed the coast. Then the prey moved south, the old predator collapsed, and the northern branch survived."

The important outcome is not a score. It is a **specific history that belongs to this world**.

The world may continue beyond 90 minutes. The 60–90 minute arc is a minimum target for a compelling continuous session, not a hard game-over timer.

---

## 2. Core interaction loop

The player repeatedly moves through this loop:

1. **Observe** — notice a meaningful change or emerging pressure.
2. **Explain** — inspect the dominant causes behind it.
3. **Predict** — form a hypothesis about what will happen next.
4. **Commit** — intervene indirectly, or deliberately choose not to intervene.
5. **Advance time** — let the autonomous simulation respond.
6. **Compare** — see whether the prediction was confirmed, complicated, or disproved.
7. **Record** — important consequences enter the world's history and lineage record.
8. Return to observation with new information.

The emotional unit of gameplay is therefore not "press button → receive reward." It is:

> **I think this will happen → I change or preserve the conditions → the world answers me.**

The player should never need to constantly click to keep the ecosystem alive.

If the player stops touching the screen while the simulation is running, the world continues making meaningful progress. Observation is active because the player is choosing what to watch, what they think will happen, and whether an intervention is worth spending.

### Predictions are lightweight

The first version does not need a formal prediction minigame or betting system.

A prediction can simply exist in the player's head, supported by UI that makes causes and trajectories visible. Later versions may allow the player to pin a species, region, or emerging situation to a watchlist.

The design goal is cognitive participation, not extra button presses.

---

## 3. What the player controls

The first playable design should use a small intervention palette.

### Climate intervention

Change temperature or moisture in a region over time.

Examples:

- make a dry basin gradually wetter;
- cool a northern landmass;
- increase rainfall along one coast.

This is **not an instant biome paint tool**. Conditions shift over simulated time and species react according to their traits.

### Geological intervention

Change connectivity or terrain slowly.

Examples:

- raise a mountain barrier;
- lower a land bridge;
- create or enlarge an island;
- open a migration corridor.

The value of geology is primarily evolutionary: it changes migration and isolation.

### Disturbance intervention

Create a temporary environmental shock.

Candidate examples:

- drought;
- wildfire;
- volcanic disturbance.

This is deliberately limited in the first design. Disturbances should create ecological opportunity and pressure, not act as a direct "kill species" button.

### Future candidate: assisted migration

Move a **small fraction** of a population to a new region.

This can create founder effects and new evolutionary paths, but it is more direct than the preferred fantasy and should not be included until we prove it adds more than it removes.

---

## 4. Intervention resource

Working name: **Influence**.

Influence exists to prevent the player from constantly correcting every unwanted consequence.

Design goals:

- interventions are meaningful decisions, not paintbrush spam;
- the player sometimes must accept extinction, collapse, or an unwanted evolutionary direction;
- there is enough consequence time between major interventions;
- the resource must never turn the game into a real-time waiting timer;
- the optimal strategy must not be "farm notifications/species to earn more buttons."

Initial hypothesis:

- Influence has a small cap;
- large interventions cost meaningfully more than local nudges;
- replenishment is tied primarily to simulation progress / world epochs rather than real-world clock waiting;
- notable discoveries may occasionally grant a small bonus, but cannot become the dominant farming strategy;
- choosing **not** to intervene is a legitimate strategic action because saved capacity remains valuable.

Important rule:

> Influence buys the right to alter conditions, never the right to dictate a biological outcome.

Exact economy is deferred until the simulation exists.

---

## 5. Time controls

The world runs continuously.

Candidate controls:

- Pause
- 1×
- 4×
- 16× or equivalent "fast epoch" speed

The player should frequently change speed:

- fast while waiting for slow ecological change;
- normal while tracking a migration/collapse;
- pause while inspecting causality or choosing an intervention.

Simulation correctness must not depend on render frame rate or selected presentation speed.

---

## 5.5. Emerging story threads

The simulation should continuously detect a small number of **emerging situations worth watching**.

Working name: **Threads**.

Threads are not authored quests and do not force outcomes. They are summaries of real simulation states that already exist.

Candidate examples:

- a population is approaching a climate boundary;
- two regional populations are losing gene flow and beginning to diverge;
- a predator is following prey into a new region;
- a lineage is trapped in a shrinking refuge;
- a newly opened corridor may reconnect two populations;
- rapid resource depletion is creating a local collapse risk;
- an island population has become ecologically isolated.

The player may pin a few Threads to follow.

A Thread ends because the underlying simulation resolves it:

- migration succeeds or fails;
- populations reconnect;
- divergence disappears;
- speciation occurs;
- a refuge stabilizes;
- extinction occurs;
- the situation becomes irrelevant.

### Why Threads exist

The autonomous world may contain hundreds of simultaneous numerical changes. The game needs to surface **interesting causality without scripting the world**.

Threads provide:

- a reason to keep watching;
- natural short-term goals without conventional quests;
- a bridge between raw simulation data and memorable history;
- a cheap systemic source of variety because the same detectors work across all seeds.

Threads must never invent a problem that is not present in the simulation.

---

## 6. Starting state

The session should begin with a world that is already alive enough to produce consequences quickly.

The player should **not** spend ten minutes waiting for abiogenesis.

The generated starting state should contain several **latent tensions** so that the first interesting question appears quickly without scripting a fixed tutorial event.

Candidate latent tensions:

- one population near the edge of its climate tolerance;
- one possible migration corridor;
- one geographically isolated or nearly isolated population;
- one locally crowded/resource-constrained region;
- at least two regions with meaningfully different ecological opportunities.

A generated world contains:

- procedural geography;
- environmental gradients;
- several ecologically distinct regions;
- producer biomass/resources;
- a small number of ancestral consumer lineages;
- enough population density for expansion to begin immediately.

Working target:

- 2–4 ancestral animal lineages;
- multiple separated resource-rich starting zones;
- bounded procedural trait variation between seeds.

The exact number of cells/regions is intentionally not fixed by this document.

---

## 7. First 0–5 minutes — Orientation and first prediction

### Player experience

The planet is moving immediately.

The player can already see:

- vegetation/resource density;
- population concentrations;
- a few moving range boundaries;
- temperature/moisture patterns;
- one or two obvious ecological pressures.

The game introduces the minimum interpretation tools:

- select species;
- show current range;
- show population trend;
- show main food source;
- show top pressure;
- show climate suitability.

Example:

> **Population: 18,420 ↑**
>
> Main pressure: overcrowding  
> Food availability: high  
> Climate suitability: 91%  
> Expansion direction: east

The UI should answer **why** before it offers advanced detail.

### Player decision

Within the first few minutes, the player receives enough Influence for one small intervention.

The world should present at least two plausible ideas, not one obvious tutorial answer.

Example:

- increase moisture near a dry corridor, potentially enabling migration;
- cool a northern basin, potentially creating a future refuge;
- do nothing and preserve Influence.

The game does not say which is correct.

### Required emotional beat

The player should form a simple prediction:

> "I think they will move through there."

Then the simulation either confirms, complicates, or disproves it.

This is the first proof of the game's fantasy.

---

## 8. Minutes 5–15 — Expansion and competition

Populations spread into new suitable regions.

The player begins seeing:

- range expansion;
- local population booms;
- resource depletion;
- competition between populations/lineages;
- early predator/prey pressure if present;
- migration fronts.

At this point the map stops feeling static.

### First systemic story

A first intervention should visibly propagate through multiple systems.

Example chain:

> More rainfall  
> → more producer biomass  
> → herbivore population boom  
> → migration into a new basin  
> → predator follows later  
> → original basin loses prey density

The game should record only the notable consequence, not every numerical tick.

### Player motivation

The player now wants to know:

- will the migration succeed?
- will the predator follow?
- will two populations reconnect?
- will a marginal region become a refuge?

This is preferable to a quest such as "reach 10,000 animals."

---

## 9. Minutes 15–30 — Isolation and adaptation

This phase introduces the evolutionary promise.

At least one lineage should encounter sustained different pressures between parts of its range.

Sources:

- mountain/rain-shadow differences;
- islands;
- climatic gradients;
- food differences;
- predator pressure;
- player-created barriers/corridors.

Separated populations begin accumulating trait divergence.

The player can inspect this as a **divergence meter/history**, not as raw DNA.

Example:

> Northern population
>
> + cold tolerance  
> + body size  
> − reproduction rate
>
> Gene flow with southern population: low

The system should make it clear that a new species has **not yet** appeared.

### Desired player thought

> "If they stay separated, these might become different species."

This creates anticipation without scripting an event.

---

## 10. Minutes 25–45 — First speciation

The target first-session pacing should make first speciation likely in this window without guaranteeing an exact minute.

When accumulated divergence plus sufficiently low gene flow crosses the speciation condition:

> **New lineage formed**

The game records:

- parent species;
- split time;
- approximate ancestral region;
- traits that diverged most;
- current population;
- current range.

The tree of life visibly branches.

### Important presentation rule

Do not present speciation as a random loot drop.

The player should be able to look backward and understand:

> barrier → isolation → different pressure → divergence → speciation

### Emotional beat

This is one of the most important moments in the first session.

The player did not press "evolve."

They created or tolerated conditions under which evolution happened.

---

## 11. Minutes 35–60 — Ecological instability

The session now has enough moving parts for success in one region to cause problems elsewhere.

Candidate emergent patterns:

- a highly successful herbivore strips producer biomass;
- a predator follows prey into a new region and overexploits it;
- an adapted lineage displaces its ancestor locally;
- warming opens one habitat while closing another;
- a migration corridor reconnects populations and slows divergence;
- a new barrier protects a lineage but traps it in a small habitat.

The player should encounter at least one problem that cannot be solved without tradeoffs.

Example:

A cold-adapted species survives only in a northern refuge.

The player can:

- cool the region and preserve it;
- increase rainfall elsewhere and try to expand its food base;
- spend Influence creating a corridor;
- accept that it may disappear and save Influence for the wider ecosystem.

No option is framed as morally or mechanically correct.

---

## 12. Minutes 50–75 — First memorable extinction or near-extinction

A serious session needs loss.

If the first hour produces only growth and new species, extinction has no emotional weight and the simulation feels like a collection game.

The target is for many worlds to create at least one:

- extinction;
- lineage reduced to a tiny refuge;
- predator collapse;
- failed colonization;
- branch that appears and later disappears.

The timeline should preserve the extinct lineage.

Example:

> **Kara borealis — extinct**
>
> Existed for 1.8 million simulated years  
> Descended from Kara minor  
> Peak population: 84,200  
> Final refuge: Northern Plateau  
> Major pressures before extinction:
> - food decline
> - warming
> - low migration access

The game should avoid pretending it can always identify one perfect "cause of extinction." It may present several strongest measured pressures.

---

## 13. Minutes 60–90 — A world with history

At this point the world should feel meaningfully different from its starting state.

Target state, not strict requirements:

- several occupied regions;
- 5–15 recognizable active lineages/species;
- at least one visible branch in the tree of life;
- one migration story;
- one ecological boom/collapse;
- one extinction or serious near-extinction;
- at least one consequence traceable to a player intervention;
- at least one important outcome that happened without player intention.

The player should have multiple simultaneous interests:

- a favorite lineage;
- a region under pressure;
- a potential future speciation;
- a species they may choose not to save;
- a world-scale climate/geography plan.

This is the transition from "I am learning the systems" to "this is my planet."

---

## 14. There is no forced ending at 90 minutes

The game should support persistent worlds.

Possible long-session motivations:

- preserve or deliberately reshape ancient lineages;
- produce high ecological diversity;
- survive major seeded climate cycles;
- explore island radiations;
- follow the full tree of life;
- create extreme environments and see what adapts;
- compare world histories across saves/seeds.

A player may eventually retire/archive a world, but retirement is not required for the first prototype.

---

## 15. How sessions become different

Replayability must be path-dependent.

### World seed variables

Candidate macro parameters:

- land/water distribution;
- fragmentation/island count;
- elevation structure;
- baseline temperature;
- moisture distribution;
- climate variability;
- long-term warming/cooling tendency;
- environmental productivity;
- geological activity.

### Starting biological variables

Within controlled bounds:

- ancestral trait profiles;
- starting population distributions;
- feeding efficiencies;
- dispersal tendency;
- climate tolerances.

### Dynamic variation

Even with similar seeds:

- migration timing;
- population bottlenecks;
- trait mutations;
- local extinctions;
- founder effects;
- player interventions.

### Critical requirement

Different sessions should not merely display different numbers.

They should produce different **strategic and historical structures**:

- continental expansion;
- island radiation;
- refuge survival;
- predator-prey oscillation;
- fragmentation and repeated speciation;
- low-diversity harsh worlds;
- highly connected stable worlds.

---

## 16. Causality and legibility

Every important event needs an explanation path.

When a population declines, the player should be able to inspect major measured pressures such as:

- food availability;
- predation pressure;
- climate mismatch;
- overcrowding/competition;
- habitat size;
- migration accessibility.

When a new species forms, the player should be able to inspect:

- parent lineage;
- separation duration;
- gene-flow trend;
- strongest trait divergence;
- regions involved.

The interface should summarize rather than expose the entire simulation equation by default.

A deep-detail/debug layer can exist separately.

---

## 17. Session pacing rules

The minute ranges in this document are **soft pacing targets, not scripted event timers**.

The simulation must not secretly force speciation at minute 30 or extinction at minute 60. Instead, world generation and starting conditions should make these outcomes reasonably likely across many runs.

If a particular world does not produce an actual speciation in the target window, it should still expose a meaningful evolutionary trajectory—for example a strongly diverging population—so the core promise remains visible without falsifying the simulation.

### Rule 0 — interest density

The player should usually have:

- at least one active Thread worth following;
- at least one plausible future intervention;
- at least one unresolved prediction.

The game should avoid both dead air and notification spam.

### Rule A — something changes before the player gets bored

At high simulation speed, the player should rarely wait more than roughly 30–90 real seconds without a visible change worth noticing.

This does not mean constant notifications.

The map itself may provide the change.

### Rule B — important events remain rare enough to matter

Speciation and extinction should not happen every minute.

A session with fifty "major" alerts has no major events.

### Rule C — interventions need consequence time

The player should not receive enough Influence to stack five major world edits before observing the first one's effect.

### Rule D — observation is active

Observation becomes gameplay because the player:

- forms predictions;
- tracks pressures;
- chooses what to protect/change;
- decides when not to intervene.

Passive waiting without interpretive decisions is not acceptable.

---

## 18. Minimal first-session UI

The first playable does not need many screens.

### Planet view

- world map;
- time controls;
- Influence;
- overlay selector;
- notable-event feed.

### Species inspector

- population;
- trend;
- range;
- main food;
- major pressures;
- core traits;
- lineage parent/children;
- divergence by regional population.

### Region inspector

- climate;
- resources;
- occupying species;
- incoming/outgoing migration pressure.

### Tree of life

- ancestry;
- living/extinct state;
- split times;
- selected-lineage focus.

### World timeline

Only notable events:

- major migration;
- speciation;
- extinction;
- major intervention;
- substantial climate/geology milestone.

### Thread/watch panel

A small optional panel for currently interesting situations:

- what is happening;
- why the system considers it notable;
- what variables are currently driving it;
- whether the player has pinned it.

This panel does not prescribe a solution.

---

## 19. Prototype success criteria

The first design/prototype should be considered promising only if repeated seeded runs can demonstrate the following.

### Autonomy

A world left alone for a long simulation period produces migrations, population changes, and some lineage turnover without player input.

### Diversity of histories

Different seeds do not all converge to the same ecological arrangement.

### Explainability

A developer/player can inspect a major boom, collapse, migration, or speciation and identify plausible dominant simulation pressures.

### Intervention consequence

A small indirect intervention can create a measurable chain of consequences without guaranteeing a target species/outcome.

### Cognitive engagement

During a 20–30 minute observation sample, the player can repeatedly state a prediction or decision such as:

- "I expect this population to migrate";
- "I think these branches will diverge";
- "I will save Influence and accept this collapse";
- "I will change rainfall here and see whether prey expansion pulls the predator with it."

If play consists mainly of waiting for alerts, the loop has failed.

### Thread quality

System-detected Threads correspond to real measurable state, resolve naturally, and help the player discover stories without becoming disguised quests.

### Loss

Extinction is possible and not always preventable.

### Stability

Not all worlds rapidly collapse into total extinction, and not all worlds approach an unchanging equilibrium.

### Performance architecture

These tests can run headlessly and much faster than real-time.

---

## 20. Explicitly deferred

Not required to validate this session arc:

- individual animal AI;
- realistic molecular genetics;
- sexual selection;
- disease;
- parasites;
- symbiosis;
- oceans with full food webs;
- civilization;
- procedural 3D bodies;
- detailed seasonal migration;
- offline progression;
- advertising reward specifics.

Any of these may become valuable later, but none should be allowed to delay proof of the central loop.

---

## 21. Design decisions strengthened in v0.2

The following direction is now preferred for subsequent M0 work:

1. The fundamental gameplay unit is **prediction → indirect action/inaction → autonomous consequence**.
2. The game should surface systemic **Threads** to solve the "interesting simulation but passive game" problem.
3. Session timing is a set of soft probability/pacing targets, never hidden scripted timers.
4. Influence must not use real-time waiting as its primary replenishment model.
5. Starting worlds should contain latent tensions that create immediate questions without forcing identical openings.

These remain subject to prototype validation, but subsequent design should assume them unless contradicted by testing.

---

## 22. Main unresolved design decisions

The next design documents should resolve:

1. What exact simulation entities own state?
2. Is the world best represented as a grid, region graph, or hybrid?
3. What does one ecological tick represent?
4. What does one evolutionary generation represent?
5. What minimum variables determine regional carrying capacity?
6. How is predation modeled without unstable numerical explosions?
7. What exactly drives migration?
8. How are heritable population traits represented?
9. How is divergence accumulated?
10. What precise condition creates a new species?
11. What event-selection rules decide what appears in the timeline?
12. What is the first Influence economy?
13. How much player causality should be visible versus inferred?
14. What quantitative metrics distinguish "interesting instability" from broken simulation?
15. Which Thread detectors are essential for the first playable?
16. How many simultaneous Threads can the UI surface before they become noise?
17. How exactly does Influence replenish from simulation progress without encouraging blind maximum-speed fast-forward?

These questions define the remainder of M0.
