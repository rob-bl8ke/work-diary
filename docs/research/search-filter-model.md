# Search and Filter Model

> **Ticket:** [rob-bl8ke/work-diary#20](https://github.com/rob-bl8ke/work-diary/issues/20)
> **Date:** 2026-10-02

---

## Decision Summary

Search and browse are a single unified view. Applying no filters gives a newest-first chronological timeline; applying filters narrows it. The main zone uses a Kindle-style navigation model: library state (search + results) and reading/editing state (full-zone document) are two distinct modes with a clean transition between them. On-demand tabs provide future multi-artifact support without permanently introducing IDE chrome.

---

## Filter Dimensions

| Dimension | Type | Notes |
|---|---|---|
| `type` | Multi-select chip | event / note / adr / plan / snapshot |
| `tags` | Multi-select chip | dynamic list from current result set |
| `date range` | Preset + custom | see Date Range UX below |
| `service` | Multi-select chip | dynamic list from current result set |

**Composition rule**: dimensions combine with AND; multiple selections within a single dimension combine with OR. Example: `type = [adr OR plan] AND tags = [architecture] AND date = [this month]`.

---

## Content Search

FTS5 full-text search across:
- `title` (frontmatter)
- `tags` (frontmatter, stored as space-separated string in the index)
- `service` (frontmatter)
- body (full markdown content, stripped of syntax)

One query box. No separate "field search" vs "body search" modes.

---

## Result Ranking

| State | Sort |
|---|---|
| Text query present | FTS5 BM25 relevance (best match first) |
| No query (browsing) | Newest-first by `date` frontmatter field |

The sort switches automatically; no UI control is needed.

---

## Result Item Appearance

Each result item shows:
- **Title** (prominent)
- **Date** (quiet, secondary)
- **Type badge** (small chip — `adr`, `event`, etc.)
- **Tag chips** (all tags on the entry)
- **FTS5 body snippet** (~2 lines of matched context, highlighted match term)

The snippet is the differentiator when multiple entries share title words.

---

## Date Range UX

A small control with:
- **Relative presets**: Today / This week / This month / Last 30 days / Last 90 days
- **Custom range**: date-from + date-to inputs, triggered by "Custom…" option

Presets cover the common navigation patterns (what did I do this week/month) in one click.

---

## Tag and Service Filter UX

Both use the same **dynamic chip list** pattern:
- All values present in the *current* result set are shown as clickable chips
- Clicking a chip toggles it on/off; active chips are visually highlighted
- The list contracts as other filters narrow the result set — you only see values that exist in the remaining results

This is context-aware faceted navigation. The tag vocabulary is not a fixed global list; it reflects what is currently reachable.

---

## List Handling

Results are rendered with **TanStack Virtual** (windowed virtual list). Only visible rows are in the DOM. No pagination, no load-more button. Handles 10,000+ entries without DOM bloat.

Pairs naturally with TanStack Query (already in the stack).

---

## Filter State Persistence

Active filters and query text are stored in **URL query params**.

- Page refresh restores exact filter state
- Browser back/forward navigates filter history
- A filtered URL can be pasted into a note as a reference link
- Implemented via TanStack Router (or React Router v7) URL search param sync

---

## Main Zone Layout Model

### v1 — Kindle navigation

The main zone operates in two mutually exclusive states:

**Library state** (default / home):
- Search input + filter bar at the top
- Virtualised result list below
- URL: `/` or `/?q=...&type=adr&tags=architecture`

**Reading / editing state**:
- Full main zone devoted to the document
- Rendered Markdown view (reading) or Monaco editor (editing)
- No search bar, no filter bar, no persistent chrome
- A quiet breadcrumb / ← back link at the top-left returns to the library
- URL: `/entries/2026/10/2026-10-02T1430-some-adr`
- Browser back returns to the library at the previous filter state (URL params preserved)

The left nav remains visible in both states but visually recedes during reading (quiet navigation, per `docs/uix.md §2.5`). The right context drawer is available in reading state for AI assistance and metadata.

### Future — on-demand tabs ("open alongside")

The Kindle model is the default and permanent behaviour for single-document work. When the user triggers **"Open alongside"** on a second artifact, a quiet tab strip appears above the main zone. Closing back to one artifact makes the tab strip disappear — the Kindle experience is fully restored.

Key constraints this places on v1 routing architecture:
- The router must support a "tab stack" alongside the standard library ↔ document navigation. TanStack Router's outlet nesting handles this.
- Do not implement a permanent tab strip in v1. The tab strip is a progressive layer, not baseline chrome.
- "Open alongside" is a v2 feature; the router seam for it must exist in v1.

---

## Constraints Carried Forward

- FTS5 index must cover body text, not frontmatter only — a body-only-excluded index would feel broken.
- Tag and service values in the chip list are driven by the live index, not a hardcoded vocabulary.
- The router architecture must leave room for on-demand tabs without a structural rewrite.
- The reading state must be fully clean — no search or filter chrome visible — to honour the Kindle/editorial aesthetic.
