# ADR-0001: GitHub is the durable project source of truth

Status: **Accepted**  
Date: 2026-09-22

## Context

The project is being developed with substantial AI assistance. Individual chat sessions are transient: they may end, lose context, hit limits, or be replaced.

Relying on conversational memory would make continuity fragile and could cause design and implementation decisions to be lost or contradicted.

## Decision

The Genetix repository is the durable source of truth for:

- current project state;
- game design;
- architecture;
- significant decisions;
- technical debt;
- roadmap;
- implementation.

`START_HERE.md` is the canonical re-entry point for new AI sessions and developers.

A human-facing Google Sheets mirror will summarize operational project status, but it does not replace repository technical/design truth.

## Consequences

Positive:

- project continuity does not depend on one chat;
- new sessions can recover context;
- architectural reasoning remains reviewable;
- developers can understand why decisions were made.

Cost:

- meaningful work requires documentation maintenance;
- stale docs are treated as a defect and must be corrected.
