# ADR-002: Tag Strategy — SemVer + Rolling

Date: 2026-04-24
Status: Accepted

## Decision

Every git tag `vX.Y.Z` produces three GHCR tags:

- `X.Y.Z` — immutable, for production pinning
- `X.Y` — rolling minor, auto-updates to newest `X.Y.*` patch
- `latest` — rolling, auto-updates to newest release

## Rules

- Business projects (Helios / Helixa / Tide) **must** pin `X.Y.Z` in Dockerfiles.
- `latest` is for experiments only. Never in production.
- `X.Y` is a compromise for CI/staging: auto-get bugfixes, no surprise major upgrade.

## Version bump rules

- **PATCH** (`0.1.0 → 0.1.1`): library version bumps, CVE fixes, docs.
- **MINOR** (`0.1.0 → 0.2.0`): new image added, new library added to existing image,
  optional breaking change behind feature flag.
- **MAJOR** (`0.x.x → 1.0.0`): Python version upgrade (3.14 → 3.15), removed
  library, changed default user, removed image from tree.

## Upgrade protocol for business projects

1. Read CHANGELOG.md, verify no breaking change affects you.
2. Update `FROM` in your Dockerfile to new version.
3. Rebuild locally, run smoke tests.
4. Commit + deploy.

Never auto-upgrade business projects when bases change. Always explicit.
