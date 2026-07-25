# Contributing to CityPulse

## Development Workflow

All implementation work should be associated with a tracked GitHub issue.

Typical flow:

    Issue
      ↓
    Branch
      ↓
    Implementation
      ↓
    Local Verification
      ↓
    Pull Request
      ↓
    CI
      ↓
    Review
      ↓
    Merge
      ↓
    Staging Verification

## Branch Naming

Feature:

    feat/CP-123-short-description

Bug fix:

    fix/CP-123-short-description

Architecture:

    arch/CP-123-short-description

Documentation:

    docs/CP-123-short-description

## Commit Convention

CityPulse uses Conventional Commit-style messages.

Examples:

    feat: add incident state machine
    fix: reject stale GPS samples
    test: add incident authorization tests
    docs: document realtime protocol
    refactor: isolate geospatial query service
    chore: configure linting

## Pull Requests

Pull requests should:

- Reference the related issue.
- Explain the outcome of the change.
- Describe API or database changes.
- Identify security/privacy implications.
- Include testing evidence.
- Document migrations and rollback implications.
- Update architecture documentation when required.

Prefer small, reviewable pull requests.

## Definition of Ready

Work should begin only when:

- The expected outcome is clear.
- Acceptance criteria are testable.
- Dependencies are understood.
- Security/data implications are identified.
- Architecture-impacting decisions have an ADR when required.

## Definition of Done

Work is complete when:

- Implementation is merged.
- CI passes.
- Appropriate tests exist.
- Authorization paths are tested.
- API contracts are updated.
- Database migrations are safe.
- Operational telemetry is included where required.
- Security/privacy implications are addressed.
- Documentation is current.
- The feature has been verified in the appropriate environment.

## Architecture Decisions

Significant architectural decisions must be documented under:

    docs/adr/

Do not silently introduce major infrastructure, databases, frameworks,
communication protocols, or cross-domain dependencies.

## Security

Never commit:

- Passwords
- API keys
- Access tokens
- Private certificates
- Production `.env` files
- Terraform state
- Sensitive user/location datasets

Report security concerns according to `SECURITY.md`.