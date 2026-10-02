# Work Diary — UI/UX Design Specification

## 1. Purpose

Work Diary is a personal knowledge and work-history application used to store and retrieve professional information such as:

- Architecture Decision Records
- technical notes
- work events
- plans
- designs
- investigations
- meeting notes
- implementation notes
- decisions
- research
- retrospectives
- chronological work history

The application should feel calm, focused, highly readable, and suitable for prolonged daily use.

The UI should take inspiration from the restrained, editorial qualities of applications such as Claude without attempting to reproduce any product exactly.

The intended visual character is:

> A calm, editorial productivity workspace where content receives more visual emphasis than the application controls surrounding it.

The interface should feel closer to reading and editing a well-designed document than operating a traditional enterprise application.

---

# 2. Core UX Principles

## 2.1 Content First

The user's notes, documents, ADRs, events, plans, and designs are the primary visual focus.

Application chrome should recede into the background.

When viewing a page, the eye should naturally land on the content before:

- navigation
- buttons
- menus
- metadata
- filters
- settings
- other controls

---

## 2.2 Calm Software

The application should avoid visual noise.

Prefer:

- whitespace
- typography
- subtle background differences
- restrained iconography
- lightweight dividers

over:

- boxes
- borders
- cards
- shadows
- badges
- colourful buttons
- dense toolbars

The application should never feel like an administrative dashboard.

---

## 2.3 Editorial Reading Experience

Long-form reading comfort is a first-class requirement.

Documents should resemble a well-typeset article or technical document rather than text displayed inside an application panel.

Important characteristics:

- comfortable line length
- generous line height
- strong heading hierarchy
- restrained use of bold
- good paragraph spacing
- clear lists
- clean code blocks
- readable tables
- excellent Markdown rendering

---

## 2.4 Progressive Disclosure

Do not display every available control permanently.

Secondary functionality should appear when relevant through mechanisms such as:

- hover actions
- contextual menus
- popovers
- drawers
- expandable areas
- command/search interfaces

The visible interface should represent what the user currently needs rather than everything the application can do.

---

## 2.5 Quiet Navigation

Navigation should remain predictable and easy to discover while visually subordinate to the document being viewed or edited.

The navigation should feel like a bookshelf or notebook index rather than a traditional application menu.

---

## 2.6 Chronology Is Important

Work Diary is inherently chronological.

Dates, times, recency, and relationships between entries are important pieces of information, but chronological metadata should remain visually quieter than document titles and content.

Metadata should help orient the user without dominating the page.

---

# 3. Overall Visual Direction

The interface should use a warm, neutral visual language.

Think:

> paper + ink + typography + whitespace + subtle controls

Avoid a cold, high-contrast enterprise SaaS aesthetic.

The interface should feel comfortable when viewed for several hours.

---

# 4. Colour System

Use semantic design tokens rather than hard-coding colours throughout the application.

The following palette is the preferred starting point.

## 4.1 Light Theme

```css
--canvas:            #F8F7F4;
--surface:           #F3F1EC;
--surface-subtle:    #F5F4F0;
--surface-hover:     #ECEAE4;
--surface-selected:  #E8E5DE;

--text-primary:      #2E2D29;
--text-secondary:    #66635D;
--text-muted:        #85817A;
--text-disabled:     #AAA69E;

--border-subtle:     #E2DFD8;
--border-default:    #D8D4CC;
--border-strong:     #C8C3BA;

--accent:            #C76543;
--accent-hover:      #B85C3C;
--accent-subtle:     #F3E2DA;

--success:           #557A5A;
--warning:           #A77632;
--danger:            #B5443C;
--info:              #55758C;
```

These values are guidance rather than a requirement for exact pixel matching.

The important characteristics are:

- warm off-white canvas
- warm grey surfaces
- charcoal rather than black text
- low-contrast borders
- restrained earthy accent colour
- minimal saturation

---

# 5. Contrast Philosophy

Primary content should have strong contrast.

Application chrome should use progressively lower contrast.

Recommended hierarchy:

1. document title
2. document body
3. section headings
4. primary navigation
5. metadata
6. secondary controls
7. dividers and decorative structure

Avoid using pure black except where technically required.

Avoid excessive use of pure white.

---

# 6. Typography

Typography should provide most of the visual hierarchy.

Avoid creating hierarchy primarily through boxes, colour, or decoration.

## 6.1 UI Typeface

Use a clean modern sans-serif for:

- navigation
- controls
- labels
- metadata
- menus
- search
- buttons

Suitable characteristics:

- high readability
- neutral personality
- good rendering at small sizes
- clear distinction between similar characters

---

## 6.2 Reading Typeface

Long-form rendered Markdown may use either:

- an excellent humanist sans-serif, or
- a restrained editorial serif

The reading typeface should make long-form content feel deliberately typeset.

Do not use a highly decorative serif.

---

## 6.3 Monospace Typeface

Use a high-quality monospace font for:

- code
- Markdown editing
- technical identifiers
- fenced code blocks
- inline code

The Markdown editor may use Monaco's configured editor typography.

---

# 7. Type Scale

Approximate scale:

```text
Document title       28–32px
H1                   26–30px
H2                   22–25px
H3                   18–21px
H4                   16–18px
Body                 16–18px
UI text              14–15px
Metadata              13–14px
Small labels          12–13px
Code                  14–15px
```

Avoid oversized headings.

The application should feel editorial rather than promotional.

---

# 8. Reading Typography

Recommended body treatment:

```css
font-size: 17px;
line-height: 1.6;
font-weight: 400;
```

Paragraphs should have comfortable vertical spacing.

Avoid tightly packed prose.

Aim for approximately:

```text
65–85 characters per line
```

for prose-heavy content.

---

# 9. Content Width

Do not allow rendered documents to stretch across large monitors.

Suggested ranges:

```text
Primary reading column     700–780px
General content workspace  800–950px
Wide technical content     may exceed this where necessary
```

Content such as:

- tables
- architecture diagrams
- code blocks
- Mermaid diagrams
- wide technical artefacts

may use additional width when necessary.

Prose should remain constrained.

---

# 10. Application Shell

The desktop experience should conceptually support three zones:

```text
┌───────────────┬───────────────────────────────────────┬───────────────┐
│               │                                       │               │
│ Navigation    │          Main Work Area               │ Contextual    │
│               │                                       │ Details       │
│ Search        │       Reading / Editing               │               │
│ Recent        │                                       │ Optional      │
│ Collections   │                                       │ Drawer        │
│               │                                       │               │
└───────────────┴───────────────────────────────────────┴───────────────┘
```

The right-hand contextual area should not normally be permanently visible.

The default visual hierarchy should be:

```text
navigation + document
```

rather than:

```text
navigation + document + inspector + toolbar + dashboard
```

---

# 11. Left Navigation

The left navigation should behave like a quiet workspace index.

Potential categories might visually include concepts such as:

```text
Search

New entry

Today
Recent
Timeline
Pinned

Collections / Categories

ADRs
Notes
Plans
Designs
Events

Recent entries
```

This list is illustrative rather than a functional requirement.

---

# 12. Navigation Styling

Navigation should use:

- small icons
- short labels
- minimal separators
- low visual contrast
- subtle selected state
- generous vertical breathing room

Avoid:

- thick selection bars
- large coloured backgrounds
- boxed navigation groups
- heavy nested trees
- permanently visible action buttons beside every item

Selected navigation should typically use:

```text
slightly darker surface
+
slightly stronger text
```

rather than a visually aggressive highlight.

---

# 13. Collapsible Navigation

The navigation should visually support a reduced or hidden state.

When collapsed, the reading/editing area should become the dominant experience.

The application should still make navigation easy to restore.

---

# 14. Main Reading View

The default reading view should visually resemble a document.

Example hierarchy:

```text
Architecture Decision

ADR-014 · 2 October 2026 · Architecture

Use Kafka Outbox for Domain Event Publishing

Context

...

Decision

...

Consequences

...
```

Metadata should remain understated.

The title and document content should dominate.

---

# 15. Metadata

Metadata might visually include:

- date
- time
- type
- tags
- project
- status
- related entries
- last modified date

Metadata should normally appear using:

- smaller typography
- muted text colour
- restrained spacing
- minimal iconography

Avoid turning every metadata field into a pill.

Tags may use pills when this improves recognisability, but pills should remain subtle.

---

# 16. Markdown Rendering

Rendered Markdown is a core part of the visual experience.

It should receive dedicated styling.

Support polished rendering for:

- headings
- paragraphs
- ordered lists
- unordered lists
- task lists
- blockquotes
- links
- code blocks
- inline code
- tables
- horizontal rules
- callouts where supported
- images
- diagrams where supported

Markdown should look intentional rather than like browser-default HTML.

---

# 17. Markdown Heading Rhythm

Headings should be separated primarily through whitespace.

Example:

```text
Previous paragraph.

          generous spacing

## Heading

moderate spacing

Next paragraph.
```

Avoid placing borders around headings.

Horizontal rules should be subtle.

---

# 18. Code Blocks

Code blocks should feel integrated with the reading experience.

Prefer:

- subtly differentiated background
- modest border radius
- minimal border
- syntax highlighting with restrained saturation
- optional language indicator
- contextual copy action

Avoid oversized permanent code toolbars.

Copy controls should appear unobtrusively.

---

# 19. Tables

Tables should remain visually lightweight.

Prefer:

- subtle horizontal rules
- minimal vertical borders
- comfortable cell padding
- readable typography

Avoid spreadsheet-style boxed grids unless the content specifically requires them.

---

# 20. Markdown Editing Experience

Markdown editing is an important mode of Work Diary.

The editor should visually belong to the application rather than appearing like a separate developer tool embedded inside it.

Monaco Editor may be used.

If Monaco is used, its appearance should be customised to fit the Work Diary visual language.

---

# 21. Monaco Editor Visual Treatment

Avoid Monaco's default IDE appearance where possible.

The editing experience should feel like:

> a focused Markdown writing environment powered by Monaco

rather than:

> Visual Studio Code embedded inside the application.

Configure visual treatment accordingly.

Recommended characteristics:

- warm background matching the workspace
- subdued gutter
- subtle current-line indication
- restrained syntax colours
- minimal editor chrome
- comfortable line height
- generous left/right padding
- no unnecessary minimap by default
- no unnecessary status indicators
- no excessive visual guides
- minimal scrollbar treatment

---

# 22. Markdown Editor Typography

Suggested Monaco starting point:

```text
Font size:      15–16px
Line height:    1.55–1.65 equivalent
Font family:    quality monospace
```

Markdown prose should remain comfortable to read even before rendering.

---

# 23. Editor Gutter

The gutter should be low emphasis.

Line numbers, when displayed, should use:

- muted text
- low contrast
- compact spacing

The gutter should not dominate the writing area.

---

# 24. Editor Focus

When editing, the document should remain the primary object on the screen.

Avoid surrounding Monaco with:

- thick frames
- oversized toolbars
- persistent formatting button rows
- complex panels
- IDE-style tabs unless genuinely required

---

# 25. Reading and Editing Relationship

Reading and editing modes should feel visually related.

Moving between them should not feel like switching between two different applications.

Shared qualities should include:

- similar content width
- similar typography scale
- consistent page positioning
- consistent background
- consistent surrounding navigation

The transition should feel spatially stable.

---

# 26. Buttons

Buttons should follow a hierarchy of visual importance.

## Primary Action

Use sparingly.

Examples:

```text
Save
Create
Confirm
```

Treatment:

- restrained accent fill
- moderate radius
- strong text contrast

There should rarely be more than one visually dominant primary button in the same immediate context.

---

## Secondary Actions

Examples:

```text
Edit
Share
Export
Preview
```

Treatment:

- text or icon plus text
- transparent or subtle surface
- low-emphasis border where necessary

---

## Contextual Actions

Examples:

```text
⋯
Copy
Pin
Duplicate
```

These should often appear:

- on hover
- on focus
- inside contextual menus

rather than remain permanently prominent.

---

# 27. Button Shape

Recommended:

```text
Standard buttons     6–8px radius
Inputs               8–10px radius
Panels               10–14px radius
```

Avoid making every control fully rounded.

Pills should be reserved for concepts that genuinely benefit from pill presentation, such as compact tags or filters.

---

# 28. Icons

Use a coherent lightweight icon system.

Suitable characteristics:

```text
Size:           ~16–18px
Stroke:         ~1.5–2px
Style:          simple outline
```

Possible icon families include:

- Lucide
- Phosphor
- similarly restrained systems

Use icons to aid recognition, not as decoration.

Prefer text labels when an icon alone would be ambiguous.

---

# 29. Search

Search is a first-class Work Diary interaction.

Visually, search should feel closer to:

- document search
- Spotlight
- command palette
- knowledge retrieval

than a traditional database search form.

Example:

```text
Search work diary...

kafka retry

Architecture
  Kafka retry strategy
  2 October · ADR

Notes
  Kafka consumer failure investigation
  29 September · Note

Events
  Production retry discussion
  26 September · Event
```

---

# 30. Search Controls

Avoid a large permanent form containing many search fields.

Prefer:

```text
Search query                     Filters
```

with filters progressively disclosed.

Possible filtering controls should visually remain subordinate to results.

---

# 31. Search Results

Search results should emphasize:

1. title
2. relevant text snippet
3. date
4. type/category
5. tags or project context where useful

Chronology should be easy to understand.

Highlight query matches carefully without creating visual clutter.

---

# 32. Timeline / Chronological Views

Chronological views should feel like a readable journal or activity history rather than an analytics dashboard.

Prefer:

```text
2 October 2026

09:10
ADR
Kafka outbox strategy

11:30
Note
Discussion with architecture team

14:15
Plan
Consumer retry implementation


1 October 2026

...
```

Use spacing and typography to communicate grouping.

Avoid excessive cards.

---

# 33. Right-Side Context Drawer

Secondary information may appear in a right-side contextual drawer.

Suitable content could visually include:

- metadata
- relationships
- tags
- related entries
- document details
- history
- contextual settings

The drawer should feel temporary and contextual.

It should not permanently compress the reading area unless the user intentionally keeps it open.

---

# 34. Drawers

Drawers should use:

- subtle border separation
- consistent warm surface
- restrained typography
- low visual weight

Avoid heavy modal-like shadows.

The page should still feel spatially connected to the drawer.

---

# 35. Menus and Popovers

Menus should be compact and quiet.

Use:

- short labels
- lightweight icons
- modest spacing
- subtle hover states
- minimal borders/shadows

Avoid oversized floating panels.

---

# 36. Hover Behaviour

Hover should reveal capability.

Example:

Normal:

```text
Kafka Outbox Strategy
2 October 2026 · ADR
```

Hover:

```text
Kafka Outbox Strategy                    ☆   ⋯
2 October 2026 · ADR
```

Do not permanently show actions beside every item.

---

# 37. Focus Behaviour

Keyboard focus must remain clearly visible.

Do not sacrifice accessibility for visual minimalism.

Focus indicators should be:

- visible
- consistent
- restrained
- sufficiently contrasted

---

# 38. Spacing System

Use a consistent spacing scale.

Suggested scale:

```text
4px     micro relationship
8px     icon/text relationship
12px    tightly related controls
16px    normal UI spacing
24px    component separation
32px    section separation
48px    major section separation
64px    page-level separation
```

Use spacing rather than borders wherever possible.

---

# 39. Whitespace Rule

When deciding how to visually group content:

1. try spacing first
2. then subtle background differentiation
3. then a divider
4. use a box only when the grouping genuinely benefits from containment

---

# 40. Cards

Cards should be used sparingly.

Do not automatically place:

- notes
- metadata
- search results
- sections
- navigation groups

inside cards.

Cards should represent meaningful contained objects, not merely provide decoration.

---

# 41. Borders

Borders should almost disappear.

Recommended style:

```css
border: 1px solid var(--border-subtle);
```

Avoid:

- dark outlines
- double borders
- heavy grids
- thick section separators

---

# 42. Shadows

Shadows should be rare and subtle.

Suitable uses:

- floating menu
- popover
- temporarily elevated panel

Avoid using shadows to make every surface appear elevated.

The application should feel mostly flat and paper-like.

---

# 43. Motion

Motion should communicate state rather than entertain.

Recommended duration:

```text
120–200ms
```

Suitable uses:

- hover transition
- drawer opening
- popover appearance
- sidebar collapse
- selection change

Avoid:

- bouncing
- dramatic scaling
- large sliding page transitions
- decorative motion

---

# 44. Empty States

Empty states should be calm and helpful.

Avoid large illustrations unless they meaningfully improve the experience.

Prefer:

```text
No ADRs yet.

Architecture decisions you record will appear here.

Create ADR
```

over highly promotional onboarding UI.

---

# 45. Error States

Errors should be:

- clear
- specific
- calm
- recoverable

Avoid alarmist visual treatment unless the error is genuinely destructive or dangerous.

---

# 46. Destructive Actions

Destructive controls should not dominate normal workflows.

Use danger colouring only when required.

Delete actions should often live inside a contextual menu rather than beside normal actions.

---

# 47. Settings

Settings should feel like readable content rather than a control panel.

Example:

```text
Appearance

Choose how Work Diary appears on this device.

Theme
System                                    ▾


Reading

Control how entries are displayed.

Text size
Comfortable                               ▾


Editor

Configure the Markdown editing experience.

Show line numbers                         [toggle]
```

Use plain-language descriptions where useful.

---

# 48. Accessibility

Accessibility is part of the design system.

The interface must support:

- keyboard navigation
- visible focus states
- semantic HTML
- screen-reader labels
- sufficient colour contrast
- non-colour indicators of state
- suitable hit targets
- reduced-motion preferences
- zoom and text scaling

Minimal visual styling must never make controls undiscoverable to keyboard or assistive-technology users.

---

# 49. Responsive Behaviour

The application is primarily a desktop productivity application, but layouts should degrade gracefully.

General behaviour:

### Wide desktop

```text
Navigation | Main document | Optional context drawer
```

### Medium width

```text
Navigation | Main document
```

Context drawer overlays or appears temporarily.

### Narrow width

```text
Main document
```

Navigation and secondary panels become temporary overlays/drawers.

The reading experience should remain the priority.

---

# 50. Visual Stability

Avoid unnecessary layout movement.

Reading and editing areas should maintain stable positions when:

- switching mode
- opening menus
- showing metadata
- displaying contextual actions

The interface should feel physically predictable.

---

# 51. Loading States

Loading indicators should be subtle.

Prefer:

- skeleton text where useful
- small progress indicators
- retained page structure

Avoid large full-screen spinners for normal interactions.

---

# 52. Visual Density

Default to low-to-medium visual density.

This does not mean wasting space.

Information should remain efficient, but unrelated concepts should have sufficient separation.

The user should be able to scan the interface quickly without decoding dense UI chrome.

---

# 53. Design Anti-Patterns

Avoid the following unless there is a strong functional justification.

## Avoid Dashboarditis

Do not turn Work Diary into:

```text
12 cards
6 widgets
3 charts
4 coloured summaries
```

The application is a knowledge workspace, not an analytics dashboard.

---

## Avoid Excessive Cards

Do not wrap every logical section inside a rounded rectangle.

---

## Avoid Excessive Pills

Not every:

- type
- status
- date
- filter
- category

needs to become a pill.

---

## Avoid Excessive Toolbars

Do not create large persistent toolbar rows for actions that are only occasionally needed.

---

## Avoid Colour Coding Everything

Colour should have meaning.

Use muted typography and layout for hierarchy before adding colour.

---

## Avoid IDE Chrome Around Markdown

Even if Monaco is used, Work Diary should not look like a developer IDE.

---

## Avoid Dense Enterprise Forms

Use readable groups, explanatory text, and progressive disclosure.

---

## Avoid Visual Competition

Multiple elements should not simultaneously demand attention.

At most one primary action should normally dominate a local context.

---

# 54. Desired Emotional Qualities

The interface should feel:

- calm
- thoughtful
- intelligent
- focused
- reliable
- private
- organised
- readable
- mature
- unobtrusive

It should not feel:

- playful
- flashy
- futuristic
- gamified
- corporate
- overly technical
- dashboard-heavy
- visually busy

---

# 55. Design Evaluation Test

When reviewing any screen, perform the following checks.

## Squint Test

Take a screenshot and squint at it.

The strongest visual areas should normally be:

1. document content
2. document title/current context
3. navigation

If buttons, borders, cards, or toolbars dominate, reduce their visual weight.

---

## Five-Second Test

After looking at the screen for five seconds, the user should understand:

- where they are
- what they are reading or editing
- how to navigate elsewhere
- how to perform the obvious next action

without needing to inspect the interface closely.

---

## Reading Test

A user should be able to read a long ADR, technical investigation, or project note for 20–30 minutes without the surrounding interface becoming visually tiring.

---

## Writing Test

A user should be able to write Markdown for an extended period without feeling that they are working inside an IDE.

---

# 56. Implementation Guidance for AI Coding Agents

When implementing UI from this specification:

Prefer the least visually invasive solution that satisfies the interaction.

Before introducing a new visible component, ask:

> Can this be communicated through typography, spacing, contextual interaction, or progressive disclosure instead?

Before adding a card, ask:

> Does this content genuinely need containment?

Before adding a permanent control, ask:

> Does the user need this visible most of the time?

Before adding colour, ask:

> Does the colour communicate meaning?

Before expanding content width, ask:

> Is wider content necessary for comprehension?

---

# 57. Priority Order

When trade-offs occur, preserve these qualities in this order:

1. readability
2. clarity
3. content focus
4. navigation predictability
5. editing comfort
6. accessibility
7. visual simplicity
8. consistency
9. compactness
10. decorative polish

---

# 58. Core Design Statement

All Work Diary UI decisions should remain consistent with this statement:

> Work Diary is a quiet, editorial workspace for reading, writing, searching, and navigating a chronological body of professional knowledge.

The content should feel permanent.

The controls should feel temporary.

The user should notice their work before they notice the software.