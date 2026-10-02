# Inbox Mechanism

> **Ticket:** [rob-bl8ke/work-diary#8](https://github.com/rob-bl8ke/work-diary/issues/8)
> **Date:** 2026-10-02

---

## Decision Summary

Events enter the system through a keyboard-triggered modal overlay. Submission writes the markdown file immediately to its permanent dated location and opens it in Monaco for further editing. There is no staging area or inbox folder.

---

## Capture Surface

A modal overlay triggered globally by `Ctrl+N` (`Cmd+N` on macOS). The modal is hidden until needed, consistent with the progressive disclosure principle in `docs/uix.md`.

**Type-specific shortcut**: `Ctrl+N` opens the modal with the type selector focused. Pressing `E`, `A`, `P`, or `N` while the modal is open pre-selects the corresponding type (event, ADR, plan, note) without a mouse click.

A visible "New entry" button also opens the modal for discoverability, but keyboard is the primary path.

---

## Required Fields at Capture

| Field | Required | Notes |
|---|---|---|
| `type` | Yes | `event` \| `note` \| `adr` \| `plan` \| `snapshot` |
| `title` | Yes | Used to generate the filename slug at creation |
| body | No | Empty file body is valid; user fleshes it out in Monaco |
| `tags` | No | Surfaced in a collapsed expandable section in the modal |
| `service` | No | Same collapsed expandable section as tags |

Tags and service are collapsed by default. The fast path is type + title only.

---

## On Submit

1. NestJS `FileService` writes the markdown file to its final path.
2. Frontmatter is populated with `id`, `date`, `type`, `title`, and empty `tags` / `service` stubs.
3. ADR files receive the body template (`## Context`, `## Decision`, `## Consequences`); all other types have an empty body.
4. The modal closes.
5. The new file opens in the three-zone shell with Monaco as the editor.
6. The `chokidar` watcher (see below) picks up the new file and adds it to the FTS index.

---

## File Path

```
{content-root}/YYYY/MM/YYYY-MM-DDTHHMM-kebab-title.md
```

- Written directly to its permanent location at submission time. No staging area, no `inbox/` folder.
- `YYYY/MM/` folder structure is created if it does not exist.
- The slug (`YYYY-MM-DDTHHMM-kebab-title`) is generated once at creation and **never changes**, even if the `title` frontmatter field is later edited. The slug is the stable identifier; the frontmatter title is the human-readable display name.

---

## Modal Abandonment

| State | Behaviour |
|---|---|
| Modal opened, nothing typed | `Escape` or click-outside dismisses silently — no confirmation |
| Modal opened, title has content | `Escape` or click-outside prompts "Discard this entry?" — one confirmation step |

No draft is persisted to disk or localStorage in either case.

---

## Direct File Editing and Index Sync

Direct file editing (VS Code, CLI, any editor) is a first-class input path alongside the capture modal.

The NestJS API server runs a `chokidar` watcher on the content root. On filesystem events:

| Event | Action |
|---|---|
| `add` | Parse frontmatter, insert new FTS record |
| `change` | Re-parse frontmatter and body, update FTS record |
| `unlink` | Remove FTS record |

The watcher is active while `npm start` is running. No manual reindex step is required for files edited outside the app.

---

## Constraints Carried Forward

- The slug is immutable — features that display or link to entries must use the slug as the stable key, not the frontmatter title.
- The `chokidar` watcher must be scoped to the content root only; watching outside it is a misconfiguration risk.
- Tags and service captured in the modal must round-trip correctly through frontmatter YAML parsing.
