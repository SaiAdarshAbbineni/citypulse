# CityPulse Security Policy

Security and privacy are core system requirements for CityPulse because the
platform may process identity, incident, operational, and location data.

## Sensitive Data

The following must be treated as sensitive:

- Authentication credentials
- Session and access tokens
- Citizen live location
- Responder location
- Location history
- Reporter identity
- Internal incident notes
- Administrative actions
- Private evidence attachments

## Secret Management

Secrets must never be committed to Git.

Local secrets belong in `.env`.

Production secrets will be managed through the approved cloud secret-management
system.

`.env.example` may contain configuration names but must never contain real
credentials.

## Location Privacy

Continuous citizen location collection must be:

- Explicitly initiated
- Purpose-bound
- Access-controlled
- Time-bounded
- Auditable where required
- Subject to defined retention policies

Authentication alone does not grant access to another user's location.

## Authorization

Server-side authorization is mandatory.

Frontend visibility controls are not security boundaries.

Access decisions may depend on:

- Role
- Resource ownership
- Department
- Geographic scope
- Operational assignment
- Action

The default policy is deny unless explicitly authorized.

## Security Testing

Security-sensitive functionality should include negative tests covering
unauthorized access and privilege boundaries.

Production releases will include automated dependency, secret, container,
and application security checks as appropriate.

## Vulnerability Reporting

Do not disclose suspected vulnerabilities publicly through GitHub issues.

Use the repository's private security-reporting mechanism when enabled.