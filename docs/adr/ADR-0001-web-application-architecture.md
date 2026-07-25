# ADR-0001: Web Application Architecture

- Status: Accepted
- Date: 2026-07-25
- Decision Owners: CityPulse Engineering

## Context

CityPulse requires a responsive web application supporting:

- operational dashboards
- interactive geospatial maps
- realtime incident updates
- live GPS visualization
- citizen workflows
- operator workflows
- analytical interfaces
- authenticated and role-specific experiences

The frontend must remain maintainable as these product domains grow.

## Requirements

The selected architecture should provide:

- strong type safety
- mature React support
- routing
- production build optimization
- server and client rendering capabilities
- good developer tooling
- support for realtime browser applications
- compatibility with mapping and visualization libraries
- maintainable feature-oriented organization

## Options Considered

### React with Vite

Advantages:

- simple architecture
- fast development tooling
- excellent SPA support
- low framework overhead

Disadvantages:

- application-level routing and rendering architecture requires additional decisions
- fewer integrated application-platform capabilities

### Next.js with React and TypeScript

Advantages:

- mature React application framework
- integrated routing
- TypeScript support
- server/client rendering options
- production optimization
- strong ecosystem
- suitable for both public and authenticated application surfaces

Disadvantages:

- additional framework complexity
- server/client boundaries require discipline
- framework features can be overused unnecessarily

## Decision

CityPulse will use Next.js with React and TypeScript for its primary web application.

## Rationale

CityPulse contains both public-facing and highly interactive authenticated
surfaces.

Next.js provides an application framework while retaining React's component
model and ecosystem.

TypeScript is mandatory for application code to reduce contract errors across
API, realtime, mapping, and UI boundaries.

## Consequences

### Positive

- Consistent frontend architecture
- Strong type safety
- Mature routing
- Production-oriented build system
- Access to the React ecosystem

### Negative

- Engineers must understand server/client component boundaries
- Framework upgrades may introduce migration work
- Some CityPulse screens will receive little benefit from server rendering

## Risks

The team may unnecessarily use framework features where normal client-side
React behavior is sufficient.

## Implementation Constraints

- TypeScript strict mode should remain enabled.
- Server state should not be duplicated unnecessarily into global client state.
- TanStack Query will manage remote/server state.
- Client-only global state should remain limited.
- Feature code should be organized by product domain.
- Map-heavy operational views may primarily execute client-side.

## Revisit When

Reconsider this decision if:

- Next.js materially blocks required realtime/map behavior,
- framework complexity becomes a significant delivery bottleneck,
- or a future native/mobile architecture changes the role of the web client.

## Related Decisions

Future API contract and realtime architecture ADRs.