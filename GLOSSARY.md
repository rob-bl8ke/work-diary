# Glossary

## Event

A time-stamped, immutable log entry recording something that happened: a decision moment, an observation, a quick note, or a standup record. Events are written once and never updated. They are the raw material from which artifacts may be derived.

**Frontmatter type value**: `event`

## Artifact

A structured, living document purposefully composed and revisited over time. Examples: ADRs, implementation plans, architectural notes, decision histories. An artifact may have a lifecycle status and may reference the events it emerged from. Unlike events, artifacts evolve — they are edited, their status changes, and they may be snapshotted.

**Frontmatter type values**: `adr`, `plan`, `note`, `snapshot`, or any custom string

## Snapshot

A frozen, point-in-time copy of an artifact at a meaningful milestone (e.g. "the state of this ADR when we shipped v2"). The live artifact continues evolving in its own file; the snapshot preserves the state at the moment it was taken. Git history provides automatic version tracking; explicit snapshots mark semantically significant points.

**Frontmatter type value**: `snapshot`  
**Key field**: `snapshot-of` — the slug of the artifact being snapshotted

## Service

The primary service, project, or system a note is associated with. Stored as the `service` frontmatter field. May be a single string (`service: my-api`) or an array for notes that cross service boundaries (`service: [my-api, platform-team]`). Omitted entirely for generic or personal notes not tied to any specific service. First-class filter dimension in the search index.

## Slug

The stable filename identifier for a note, derived from its creation date, time, and title: `YYYY-MM-DDTHHMM-kebab-title`. Example: `2026-10-02T1430-decided-on-react`. Used as the note's `id` value and as the reference key in cross-linking fields (`source-events`, `related-adrs`, `superseded-by`, `snapshot-of`). Slugs should not be changed after creation — renaming a file breaks all references to it.
