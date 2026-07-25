# CityPulse Architecture

This directory contains the version-controlled architecture documentation for
CityPulse.

The architecture documentation describes the system at multiple levels rather
than relying on a single large diagram.

## Architecture Views

| Document | Purpose | Status |
|---|---|---|
| [System Context](system-context.md) | CityPulse actors and external systems | Active |
| Container View | Major runtime/deployment components | Planned |
| Backend Component View | Django domain modules and dependencies | Planned |
| Incident Sequence | End-to-end incident workflow | Planned |
| Realtime Sequence | Live GPS and WebSocket flow | Planned |
| Deployment View | AWS production topology | Planned |

## Architecture Decisions

Significant technical decisions are maintained separately as Architecture
Decision Records:

`../adr/`

ADRs explain why architectural choices were made and the conditions under
which they should be reconsidered.

## Architecture Principles

CityPulse follows these core principles:

1. Domain boundaries before service boundaries.
2. Begin with a modular monolith.
3. PostgreSQL/PostGIS is the authoritative transactional and geospatial store.
4. Redis is used for ephemeral and cache-oriented state, not durable truth.
5. Realtime delivery does not replace authoritative state.
6. Location data is purpose-bound and privacy-sensitive.
7. Asynchronous operations must tolerate retries and duplicate delivery.
8. Infrastructure and architecture changes are version controlled.
9. Performance decisions require measurement.
10. Distributed-system complexity must be justified by actual requirements.

## Diagram Standard

Architecture diagrams are maintained primarily using Mermaid.

This keeps diagrams:

- version controlled
- reviewable in pull requests
- editable without proprietary software
- colocated with the architecture they describe

Presentation-quality exports may be generated separately when required.

## Architecture Evolution

The architecture documentation describes the current approved design.

When implementation changes an architectural assumption:

1. identify whether an ADR is required,
2. record the decision,
3. update the affected architecture view,
4. update implementation,
5. verify documentation against deployed behavior.

Architecture documentation must not knowingly describe a system that no longer
exists.