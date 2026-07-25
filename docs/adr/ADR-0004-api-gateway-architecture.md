# ADR-0004: API Gateway Architecture

- Status: Accepted
- Date: 2026-07-25
- Decision Owners: CityPulse Engineering

## Context

CityPulse initially uses a Django modular monolith, but the architecture is
designed so selected workloads may later be extracted into independently
deployed services.

The platform exposes multiple categories of network traffic:

- REST APIs
- authentication endpoints
- live GPS ingestion
- WebSocket connections
- administrative APIs
- future independently deployed services

If clients connect directly to individual backend runtimes, routing,
rate-limiting, service evolution, and cross-cutting network policies can become
coupled to application code or cloud-specific infrastructure.

CityPulse therefore requires a clearly defined ingress and API-routing
strategy.

## Requirements

The gateway layer should support:

- HTTP routing
- WebSocket proxying
- path-based upstream routing
- gateway-level rate limiting
- request controls
- centralized traffic observability
- correlation/request identifiers
- health-aware upstream routing
- future service extraction
- stable external API paths
- containerized local development
- cloud deployment
- configuration as code

The gateway must not become the owner of CityPulse business authorization.

## Options Considered

### Option 1: Direct Application Ingress

Traffic is routed directly from the external load balancer to Django.

Example:

    Client
       |
       v
      ALB
       |
       v
    Django

Advantages:

- simplest architecture
- lowest operational overhead
- fewer infrastructure components
- sufficient for a small application

Disadvantages:

- gateway concerns can leak into application code
- future service extraction changes ingress architecture
- less portable gateway policy management
- centralized traffic policies become harder as runtimes increase

### Option 2: AWS-Native Gateway / Load Balancer Routing

Use AWS-managed ingress capabilities such as Application Load Balancer and,
where required, AWS API Gateway.

Advantages:

- managed AWS infrastructure
- reduced operational responsibility
- strong integration with AWS
- autoscaling and managed availability

Disadvantages:

- stronger cloud coupling
- local development differs substantially from production
- API Gateway introduces another managed-service model and cost structure
- portability of gateway configuration is reduced

### Option 3: Apache APISIX

Deploy Apache APISIX as the application API gateway behind the external edge
and load-balancing layer.

Example:

    Internet
       |
       v
    CloudFront / WAF
       |
       v
      ALB
       |
       v
    Apache APISIX
       |
       +------> Django Platform API
       |
       +------> Realtime Runtime
       |
       +------> Future Extracted Services

Advantages:

- explicit API gateway boundary
- dynamic routing
- WebSocket support
- rate-limiting capabilities
- plugin architecture
- centralized gateway policy
- works in local/container and cloud environments
- reduces client coupling to backend deployment topology
- provides a natural routing layer during future service extraction

Disadvantages:

- additional production runtime
- additional configuration
- another component requiring monitoring and upgrades
- incorrect use could duplicate responsibilities already handled by ALB,
  WAF, or Django
- introduces operational cost before multiple backend services exist

## Decision

CityPulse will use Apache APISIX as its application API gateway.

APISIX will sit behind the external AWS edge/load-balancing infrastructure and
in front of CityPulse application runtimes.

The initial routing model will remain intentionally small.

Example:

    /api/v1/*  -> Django Platform API
    /ws/*      -> Django Channels / ASGI runtime

As independently deployed services are introduced, routing may evolve without
requiring clients to understand internal deployment topology.

Example:

    /api/v1/incidents/* -> Platform
    /api/v1/dispatch/*  -> Platform
    /api/v1/location/*  -> Location Service
    /ws/*                -> Realtime Service

## Responsibility Boundary

### CloudFront / WAF

Responsible for edge concerns such as:

- CDN behavior where applicable
- perimeter filtering
- web application firewall controls
- coarse abuse protection

### Application Load Balancer

Responsible for:

- AWS ingress
- target connectivity
- infrastructure-level load balancing
- health-aware traffic delivery

### Apache APISIX

Responsible for:

- application-level upstream routing
- API path routing
- WebSocket routing
- gateway-level rate limits
- request/traffic policies
- gateway telemetry
- future service-routing abstraction

### Django / DRF

Responsible for:

- endpoint behavior
- authentication integration
- domain authorization
- validation
- business rules
- transactions
- resource ownership
- department/geographic access policies

## Authorization Rule

APISIX must not be treated as the authoritative business-authorization layer.

For example, the gateway may determine that:

    /api/v1/incidents/*

is a valid route.

It must not determine that:

    Operator X may modify Incident Y because the incident belongs
    to Operator X's department.

That decision belongs to the CityPulse domain/application layer.

## Rate Limiting

Gateway-level rate limiting may be applied according to endpoint risk and
traffic characteristics.

Examples include:

    /api/v1/auth/*
        strict authentication abuse limits

    /api/v1/incidents/*
        normal API limits

    /api/v1/location/*
        workload-specific ingestion limits

Rate limits must be measured and configured according to legitimate workload
requirements rather than arbitrary values.

## WebSocket Routing

APISIX will support routing WebSocket traffic to the appropriate ASGI runtime.

Example:

    /ws/* -> realtime upstream

Connection authentication and subscription authorization remain application
responsibilities.

## Observability

Gateway telemetry should include:

- request count
- response status
- request latency
- upstream latency
- route
- upstream health
- rate-limit events

Correlation identifiers should propagate through:

    Client
      ->
    APISIX
      ->
    Django / Realtime
      ->
    Celery where applicable

This allows a request or operation to be traced across system boundaries.

## Configuration Management

APISIX configuration must be version controlled.

Local development and production may use different infrastructure mechanisms,
but route intent and policy must remain reproducible.

Manual undocumented production routing changes are prohibited.

## Failure Behavior

If APISIX becomes unavailable, application traffic routed through it becomes
unavailable.

Therefore:

- gateway health must be monitored
- production deployment must avoid a single gateway instance
- configuration changes must be validated before promotion
- rollback procedures must exist
- readiness and liveness checks must be configured

## Consequences

### Positive

- Stable external API boundary
- Centralized application routing
- Cleaner future service extraction
- Consistent gateway concepts across local and cloud environments
- Centralized traffic policies
- Explicit separation between gateway and domain responsibilities

### Negative

- Increased infrastructure complexity
- Additional operational knowledge required
- Additional failure boundary
- Some capabilities overlap with AWS WAF and ALB
- Initial modular-monolith deployment does not strictly require an API gateway

## Implementation Constraints

- Business authorization must remain in backend services.
- APISIX configuration must be version controlled.
- Gateway plugins must have documented purposes.
- Do not add plugins merely because they are available.
- Route changes require review.
- Gateway telemetry must integrate with CityPulse observability.
- Production APISIX must not be deployed as an unmanaged single point of
  failure.
- Internal service addresses must not be exposed to clients.
- Client API contracts must remain independent of internal service topology.

## Revisit When

Reconsider this decision if:

- APISIX operational overhead materially exceeds its value,
- AWS-native routing provides all required capabilities more simply,
- gateway latency becomes significant,
- platform architecture changes substantially,
- or another gateway provides a demonstrably better operational fit.

## Related Decisions

- ADR-0002: Backend Architecture
- ADR-0003: Primary Transactional and Geospatial Database
- Future service extraction ADR
- Future observability ADR