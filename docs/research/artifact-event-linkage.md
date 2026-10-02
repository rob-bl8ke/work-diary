# Artifact ↔ Event Linkage

> **Ticket:** [rob-bl8ke/work-diary#14](https://github.com/rob-bl8ke/work-diary/issues/14)
> **Date:** 2026-10-02

---

## Decision Summary

Artifacts reference their source events via a `source-events` frontmatter array of slugs. The link is unidirectional — events are never updated to record downstream artifacts. Reverse lookup is answered by querying the FTS index. Both directions are surfaced in the context drawer (right zone) during reading and editing.

---

## `source-events` Frontmatter Field

### Format

Each entry in `source-events` is a **slug** — the filename stem without extension or path.

```yaml
source-events:
  - 2026-09-28T0910-nestjs-adoption-discussion
  - 2026-09-29T1530-monorepo-tooling-spike
```

The full file path is mechanically derivable: `YYYY/MM/<slug>.md`. Slugs are stable identifiers because filenames are immutable after creation (per `docs/research/inbox-mechanism.md`).

### Which artifact types carry this field

| Type | `source-events` | Notes |
|---|---|---|
| `adr` | Yes | The primary use case |
| `plan` | Yes | Plans often synthesise multiple work events |
| `note` | Yes | Optional; a note may or may not derive from logged events |
| `snapshot` | No | Snapshots use `snapshot-of` / `snapshot-date` instead (see #24) |
| `event` | No | Events are the source; they do not link to other events via this field |

---

## Link Direction

**Unidirectional: artifact → events.**

Event files are never updated once written. They record what happened and remain unchanged — appending `derived-artifacts` back-references would make events mutable and conflict with the event-sourcing principle that events are the immutable source of truth.

**Reverse lookup** (which artifacts reference a given event) is answered by querying the SQLite index:

```sql
SELECT slug, title, type
FROM entries
WHERE source_events LIKE '%<event-slug>%'
  AND type IN ('adr', 'plan', 'note');
```

A dedicated relational `links` table (artifact_slug → event_slug pairs) populated at index time is the preferred implementation over raw LIKE matching — it supports exact joins and avoids false positives from partial slug matches.

---

## Fan-in and Fan-out

| Pattern | Expression |
|---|---|
| One artifact ← many events | `source-events` array with multiple slugs |
| One event → many artifacts | Multiple artifacts each carry the event slug in their `source-events`; reverse lookup returns all of them |

No special syntax is needed beyond the array. The index handles fan-out queries.

---

## UI Surface — Context Drawer

Both directions are surfaced in the context drawer (right zone) during reading and editing state. The context drawer does not require navigating away from the current document.

### Artifact reading/editing state

**"Source Events"** section in the context drawer:
- Lists each slug from `source-events` with the event's date and title (resolved from the index)
- Each item is a link — clicking navigates to the event's reading state (browser back returns to the artifact)
- If `source-events` is empty, the section shows "No source events linked" with an "Add link" affordance

### Event reading state

**"Referenced in"** section in the context drawer:
- Derived from an index query (not from the event's frontmatter)
- Lists artifacts that cite this event, with type badge, date, and title
- Each item navigates to the artifact's reading state

---

## Link Creation UX

Links are added in the **edit phase only** — not in the quick-capture modal. The modal's job is fast capture; linking is a reflective act done while writing the artifact.

**Primary path — link picker in the context drawer:**
1. While editing an artifact in Monaco, the context drawer shows the "Source Events" section.
2. An "Add link" button opens a mini search within the drawer (FTS, events only).
3. The user types a fragment of the event title or date; matching events appear as a list.
4. Selecting an event appends the slug to the `source-events` frontmatter array automatically.

**Power-user escape hatch — direct YAML editing in Monaco:**
The user can type or paste slugs directly into the `source-events` array in the frontmatter. No validation at edit time; the index will resolve (or fail to resolve) the slug on next read.

---

## Constraints Carried Forward

- The index must maintain a `links` table (artifact_slug → event_slug) populated from `source-events` frontmatter at index time; the `chokidar` watcher must update this table on file change.
- Deleting an event that is referenced by one or more artifacts creates an orphaned link. The "Source Events" drawer must handle unresolvable slugs gracefully (show slug as plain text with a "file not found" indicator rather than crashing).
- The link picker search is scoped to `type = event` only; artifacts cannot be added to `source-events`.
- `snapshot-of` linkage (snapshot → artifact) is a separate concern; see docs/research/snapshot-versioning.md (ticket #24).
