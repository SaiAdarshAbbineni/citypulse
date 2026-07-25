# CityPulse

> A real-time urban intelligence and geospatial city-operations platform.

CityPulse is an enterprise-oriented platform for monitoring city conditions,
managing incidents, tracking authorized live locations, coordinating response
operations, and deriving geospatial intelligence from urban data.

## Project Status

**Status:** Pre-implementation / Foundation

CityPulse is currently being developed from an architecture-first specification.

## Core Capabilities

- Real-time city command center
- Geospatial incident management
- Citizen incident reporting
- Live GPS and responder tracking
- Emergency dispatch workflows
- Traffic and environmental monitoring
- Geofencing and location-aware alerts
- Urban analytics and hotspot detection
- Role-based operational access
- Audit and operational observability

## Architecture

CityPulse initially follows a modular-monolith architecture with explicit
domain boundaries.

Primary technology direction:

- Next.js
- React
- TypeScript
- Django
- Django REST Framework
- PostgreSQL
- PostGIS
- Redis
- Celery
- Django Channels / WebSockets
- Docker
- Terraform
- AWS

Architecture documentation is maintained under:

`docs/01-architecture/`

Architecture decisions are maintained under:

`docs/adr/`

## Repository Structure

    apps/               User-facing applications
    services/           Backend/runtime services
    packages/           Shared internal packages and contracts
    infrastructure/     Infrastructure as Code
    deploy/             Deployment-related configuration
    docs/               Product and engineering documentation
    tests/              System-level tests
    scripts/            Engineering automation

## Environments

CityPulse is designed around four environments:

- Local
- CI
- Staging
- Production

Local development will use containerized infrastructure.

Production infrastructure is designed for AWS.

## Engineering Principles

1. Domain boundaries before service boundaries.
2. PostgreSQL/PostGIS is the durable source of truth.
3. Redis must not become authoritative storage.
4. Sensitive location data is purpose-bound and access-controlled.
5. Architecture decisions are documented through ADRs.
6. Important asynchronous operations must be idempotent.
7. Production behavior must be observable.
8. Performance claims require measurements.
9. Security and privacy are implementation requirements, not final-stage additions.
10. Complexity must be justified by requirements.

## Documentation

    docs/
    ├── 00-product/
    ├── 01-architecture/
    ├── 02-data/
    ├── 03-api/
    ├── 04-security/
    ├── 05-operations/
    ├── 06-testing/
    ├── 07-performance/
    ├── 08-runbooks/
    └── adr/

## Development

The development environment is currently being established.

A complete local bootstrap procedure will be added once the application
runtime and Docker services are initialized.

## Security

Do not commit credentials, API keys, tokens, private certificates, production
configuration, or sensitive location data.

See `SECURITY.md`.

## Contributing

Engineering workflow, branching conventions, review requirements, and
Definition of Done are documented in `CONTRIBUTING.md`.

## License

License information will be maintained in the repository root.