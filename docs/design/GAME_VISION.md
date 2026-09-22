# Game Vision

Status: **Draft v0.1**

## High concept

Genetix is a mobile ecosystem/evolution simulation where the world continuously lives, adapts, migrates, competes, speciates, and dies without direct player control.

The player intervenes in environmental conditions and evolutionary pressures, then observes and responds to the consequences.

## Core fantasy

"I touched one part of the world, and millions of years later the consequences became something I could not have directly designed."

## Desired player experience

The player should form attachment to the *history* of a generated world:

- "That species survived three climate shifts."
- "Those island populations used to be the same animal."
- "I created that mountain range, but I did not create the two species that emerged because of it."
- "That predator disappeared because the prey migrated away."
- "I tried to save one ecosystem and destabilized another."

The game should generate stories from systems rather than mostly from authored event text.

## Pillars

### Autonomous world

The simulation proceeds meaningfully when the player does nothing.

### Indirect agency

The player's strongest actions alter constraints and opportunities rather than directly commanding populations.

### Emergence

Simple rules combine into outcomes that are surprising but explainable after the fact.

### Legibility

The player must be able to understand *why* important changes happened. Complexity without explanation is noise.

### Divergent sessions

Different seeds should differ structurally through geography, climate, starting organisms, evolutionary pressures, and player choices—not only through random event cards.

### Long-session viability

A single world should support at least a 60–90 minute compelling session in the first validated design target, with architectural room for worlds that players keep much longer.

## Candidate world representation

Current direction, not final:

- medium-to-large 2D grid or region graph;
- cells/regions carry environmental values such as elevation, temperature, moisture, fertility, vegetation/resources;
- biomes are derived from conditions rather than being entirely fixed labels;
- populations are aggregated by species and region;
- visible creatures are presentation proxies, not one-to-one simulation objects.

A 64×64 grid is a useful reference scale for discussion, but is not yet a committed technical requirement.

## Candidate evolutionary representation

Species are described by a compact set of traits/parameters. Mutations change traits gradually. Persistent geographic/ecological separation plus accumulated divergence may produce speciation.

The goal is not to simulate molecular genetics. The model should create understandable evolutionary consequences at game scale.

Potential trait categories:

- body size;
- locomotion/mobility;
- reproduction;
- metabolic cost;
- thermal preference/tolerance;
- moisture preference/tolerance;
- feeding strategy;
- attack/foraging efficiency;
- defense/evasion;
- dispersal tendency.

Exact traits are not yet locked.

## Player intervention direction

Candidate categories:

- climate / rainfall;
- temperature trends;
- geological barriers and land changes;
- localized environmental disturbance;
- evolutionary pressure modifiers;
- relocation of a limited population;
- catastrophic events.

A player intervention should create consequences rather than guarantee a target result.

## Explicit non-goals for first prototype

- individual-animal simulation;
- realistic DNA/genetics;
- detailed animation;
- civilizations;
- deep disease simulation;
- ocean current simulation;
- procedural creature mesh generation;
- multiplayer;
- live service infrastructure.

These are not rejected forever. They are excluded from the first validation scope.

## Prototype question

The first prototype must answer:

> Can a small set of ecological and evolutionary rules produce a world that is interesting to observe for an extended period and whose important changes the player can understand?

If the answer is no, adding more content is not the solution; the underlying systems must improve.
