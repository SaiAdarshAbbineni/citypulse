# ADR-0003: Primary Transactional and Geospatial Database

- Status: Accepted
- Date: 2026-07-25
- Decision Owners: CityPulse Engineering

## Context

CityPulse manages strongly related operational data including users,
incidents, dispatch assignments, responders, audit records, alerts, and
geographic entities.

Location is a first-class part of the product.

The system must support spatial operations including:

- nearby searches
- distance calculations
- point-in-polygon queries
- geofencing
- map viewport queries
- road geometries
- operational zones
- spatial intersections
- incident hotspot analysis

The database must therefore provide both transactional consistency and
production-grade geospatial capabilities.

## Requirements

The primary datastore must provide:

- ACID transactions
- relational integrity
- mature indexing
- reliable migrations
- strong Django integration
- geographic data types
- spatial indexing
- distance queries
- containment queries
- intersection queries
- production backup and recovery capabilities

## Options Considered

### MongoDB

Advantages:

- flexible document model
- geospatial query support
- convenient for variable document structures

Disadvantages:

- CityPulse contains strongly relational operational data
- transactional workflows naturally map to relational models
- would introduce a second data-model philosophy without a compelling need

### PostgreSQL Without PostGIS

Advantages:

- mature relational database
- excellent transactional behavior
- strong Django integration

Disadvantages:

- latitude and longitude would largely be treated as ordinary numeric values
- advanced spatial operations would require application logic or additional tooling
- spatial indexing capabilities would be unnecessarily limited

### PostgreSQL with PostGIS

Advantages:

- ACID relational database
- mature production ecosystem
- first-class spatial data types
- spatial indexes
- distance and containment queries
- intersection operations
- strong Django GeoDjango integration
- supports both operational and geospatial data in one authoritative store

Disadvantages:

- spatial concepts require additional engineering knowledge
- incorrect coordinate reference system usage can produce incorrect results
- spatial queries require careful indexing and performance analysis

## Decision

CityPulse will use PostgreSQL with the PostGIS extension as its primary
transactional and geospatial system of record.

## Spatial Representation

Geospatial entities will use appropriate PostGIS types.

Examples:

    Citizen current position    Point
    Incident location           Point
    Response unit position      Point
    Hospital location           Point
    Road segment                LineString
    Administrative zone        Polygon / MultiPolygon
    Geofence                    Polygon / MultiPolygon
    Flood affected area         Polygon / MultiPolygon

Coordinates exchanged through external interfaces will normally use WGS84.

The coordinate reference system must be explicit rather than assumed.

## Spatial Indexing

Spatial query paths must use appropriate PostGIS indexes such as GiST where
applicable.

Indexes must be introduced according to actual query patterns rather than
added indiscriminately.

Critical spatial queries should be inspected using PostgreSQL query plans.

## Source of Truth

PostgreSQL/PostGIS is the durable authoritative source for CityPulse
transactional and geospatial state.

Redis or application caches must not become the only durable copy of
business-critical data.

## Consequences

### Positive

- Transactional and spatial data can participate in consistent workflows
- Powerful geospatial querying
- Mature operational tooling
- Strong Django integration
- Reduced need for a separate spatial database
- Future analytics can build directly on geographic relationships

### Negative

- Developers must understand spatial types and coordinate systems
- Spatial queries can become expensive when poorly bounded
- High-volume historical telemetry may eventually require partitioning,
  aggregation, or specialized storage

## Implementation Constraints

- Geographic coordinates must not be stored as strings.
- Domain entities requiring spatial behavior should use PostGIS geometry or
  geography types as appropriate.
- SRIDs must be explicitly defined.
- Spatial query endpoints must use bounded result sets.
- Map viewport APIs must not return an entire city's raw dataset.
- Appropriate spatial indexes must be created for production query paths.
- Critical spatial queries must be benchmarked.
- PostgreSQL migrations remain the authoritative schema history.
- Production database changes must never be performed manually.

## High-Volume Telemetry

Live GPS creates a distinction between current state and historical state.

The database should not automatically persist every device coordinate at the
highest possible frequency forever.

Current high-read location state may be maintained temporarily in Redis while
durable historical samples are persisted according to product and retention
requirements.

## Revisit When

Reconsider the data architecture if:

- telemetry volume materially impacts transactional workloads,
- analytical workloads interfere with OLTP performance,
- retention volume requires independent storage,
- or specialized search/stream-processing requirements emerge.

Possible future additions could include partitioned PostgreSQL tables,
analytical storage, or a dedicated telemetry pipeline.

These would complement rather than casually replace the authoritative
transactional database.

## Related Decisions

- ADR-0002: Backend Architecture
- ADR-0004: Caching and Ephemeral State
- ADR-0011: Live GPS Privacy Model