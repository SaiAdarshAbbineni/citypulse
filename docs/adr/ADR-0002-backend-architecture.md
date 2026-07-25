# ADR-0002: Backend Architecture

- Status: Accepted
- Date: 2026-07-25
- Decision Owners: CityPulse Engineering

## Context

CityPulse contains multiple business domains including identity, incidents,
geography, live location, dispatch, traffic, environment, alerts, analytics,
integrations, and audit.

The backend must support rapid initial development while preserving boundaries
that allow parts of the system to scale or deploy independently in the future.

## Requirements

The backend architecture should:

- support rapid product development
- provide strong transactional behavior
- support authentication and authorization
- integrate naturally with PostgreSQL/PostGIS
- support REST APIs
- support asynchronous processing
- support realtime communication
- maintain explicit domain boundaries
- avoid unnecessary distributed-system complexity
- permit future service extraction

## Options Considered

### Independent Microservices

Each major CityPulse domain would operate as an independently deployed service.

Advantages:

- independent deployment
- independent scaling
- strong runtime boundaries
- technology flexibility

Disadvantages:

- distributed transactions
- network failure modes
- service discovery
- more complex observability
- more infrastructure
- contract/version management
- significantly slower initial development

### Unstructured Django Monolith

All functionality would exist in one Django project without enforced domain
boundaries.

Advantages:

- very fast initial implementation
- simple deployment
- straightforward transactions

Disadvantages:

- tight coupling
- unclear ownership
- difficult testing boundaries
- increasingly difficult future service extraction

### Django Modular Monolith

CityPulse operates initially as one deployable Django platform while business
domains are implemented as explicit Django applications.

Example:

    platform/
        identity/
        geography/
        incidents/
        location/
        dispatch/
        traffic/
        environment/
        alerts/
        integrations/
        analytics/
        audit/

Advantages:

- fast initial execution
- simple deployment topology
- local ACID transactions
- explicit domain boundaries
- mature Django ecosystem
- straightforward PostgreSQL/PostGIS integration
- future service extraction remains possible

Disadvantages:

- module boundaries are not enforced by network isolation
- careless imports can create coupling
- the application initially scales as a larger deployment unit

## Decision

CityPulse will begin as a Django modular monolith using Django REST Framework
for its primary HTTP API.

Business capabilities will be separated into domain-oriented Django
applications.

These applications are modules, not microservices.

## Rationale

A modular monolith provides the best balance between development velocity and
architectural discipline for the initial CityPulse product.

Introducing distributed services before workload or organizational
requirements justify them would add operational complexity without providing
corresponding product value.

Domain boundaries will nevertheless be designed so that selected modules can
later become independently deployed services.

## Consequences

### Positive

- Fast development
- Simple local environment
- Simple initial deployment
- Strong transactional consistency
- Lower operational overhead
- Clear domain organization
- Easier debugging
- Future extraction path

### Negative

- Independent module scaling is initially limited
- Poor dependency discipline could create a tightly coupled monolith
- Deployment affects the complete backend application

## Implementation Constraints

- Each major business domain must have its own Django application.
- Cross-domain writes should use explicit application/service interfaces.
- Arbitrary cross-domain model imports should be avoided.
- Shared code must not become a generic dumping ground.
- Domain logic should not live primarily in views or serializers.
- External integrations must be isolated behind adapters.
- Cross-domain asynchronous interactions should use explicit events/tasks.
- Database ownership boundaries should remain identifiable.
- Module dependencies must be documented.

## Service Extraction Policy

A Django module should not become a microservice merely because separation is
technically possible.

Extraction requires a demonstrated reason such as:

- substantially different scaling characteristics
- independent availability requirements
- independent deployment requirements
- high-volume event ingestion
- resource isolation requirements
- security isolation requirements
- organizational ownership boundaries

## Likely Future Extraction Candidates

Potential candidates include:

    location/
    integrations/
    notifications/
    analytics/

This does not imply that extraction is currently required.

## Revisit When

Reconsider this architecture when production measurements demonstrate that a
specific domain cannot efficiently satisfy its scaling, availability,
deployment, or isolation requirements within the modular platform.

## Related Decisions

- ADR-0001: Web Application Architecture
- ADR-0003: Primary Geospatial Database
- ADR-0012: Service Extraction Policy