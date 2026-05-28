# Phase 0: DISCOVER — Clarification Protocol

This file defines the clarification questions the `feature-lifecycle` skill asks before generating specs. It is used in Phase 0, before Phase 1 (SPEC).

---

## FDD Name Check

Before all other questions, verify the feature has a canonical FDD name:

```
<Action> <Result> <Object>
```

Examples: `Calculate Total Price` · `Approve Leave Request` · `Generate Customer Statement`

If the user's request cannot be expressed in this form, make this the **first question** in the batch. The canonical name anchors all downstream naming (feature folder, use case, entity, port).

---

## When to Activate

Before Phase 1 (SPEC). Only ask questions that are **NOT** answerable from:

- Project documentation (`AGENTS.md`, existing code, existing patterns)
- The user's initial prompt / feature description
- Existing types, enums, or utilities already in the codebase

If the user's prompt already covers a topic, do not ask about it — move on.

---

## How to Ask

### Format

Present all questions in a **single batch** using the question tool. Never ask one question at a time — that wastes turns.

### Priority

Every question has a priority:

| Priority | Meaning | Action if unanswered |
|----------|---------|---------------------|
| 🔴 Blocker | Cannot generate correct specs without this answer | Skill waits for answer |
| 🟡 Default | Nice to have; skill uses sensible defaults | Skill proceeds with defaults below |

### Defaults

Applied when the user skips 🟡 questions or says "use defaults":

| Concern | Default |
|---------|---------|
| State machine | 2 states (active, closed), direct transition |
| Enum casing | lowercase |
| Action buttons | One button per allowed transition, no confirmation dialog |
| Filters | Client-side AND logic, no persistence |
| Pagination | Server-side, `page=1`, `pageSize=25` |
| Duration formatting | Check for existing utility first; else ISO diff → human-readable |
| API envelope | `{ data: T, meta: { totalCount, currentPage, pageSize, totalPages } }` |

---

## Question Catalog

### 1. State Machine

**Ask when:** the feature has entities with state-based lifecycle (status, state, phase fields).

**Questions:**

- What are the valid states? (e.g., active | closed)
- What transitions are allowed between states?
- Is there an intermediate state or a boolean flag for transitional status? (e.g., `acknowledged` as a state vs `acknowledgedAt` as a timestamp)
- Who or what can trigger each transition? (user action, system event, timeout, cron)
- Are there any guard conditions? (e.g., "can only close if acknowledged first")

### 2. Enum Values

**Ask when:** the feature has typed/sealed fields (severity, priority, category, type).

**Questions:**

- What are the allowed values for each typed field?
- Should they match an existing pattern in the codebase, or are they feature-specific?
- Are they case-sensitive? What casing convention? (lowercase, UPPERCASE, PascalCase)
- Do they map to specific UI representations? (colors, icons, badge variants, size)

### 3. Action Column / Button Logic

**Ask when:** the feature has a data table with row-level actions.

**Questions:**

- What buttons appear per row, and based on what conditions?
- What happens on each button click? (API call, state transition, navigation, modal open)
- Are there disabled or hidden states for buttons?
- Is there a confirmation step before executing? (e.g., "Are you sure?" dialog)
- Should the list refresh after a successful action?

### 4. Column Display

**Ask when:** a column's rendering is ambiguous from the field name alone.

**Questions:**

- Is this column a badge, text, link, button, icon, or number?
- Does it need formatting? (dates → human-readable, durations → semantic, currency, percentages)
- Is there a fallback display for null, empty, or zero values?
- Does the column support inline editing, or is it read-only?
- Should it be sortable?

### 5. Filter Behavior

**Ask when:** the feature has a filter bar or filterable list.

**Questions:**

- Which fields are filterable?
- Is filtering server-side (API query params) or client-side (in-memory)?
- Are multiple filters combined with AND or OR logic?
- Do filters persist across navigation? (URL params, localStorage, session)
- Is there a debounced search, or instant filtering?
- Can filters be reset? Is there a "clear all" action?

### 6. API Surface

**Ask when:** the feature communicates with a backend, and the endpoint structure is not derivable from existing conventions.

**Questions:**

- What is the base API endpoint? (e.g., `/pulse/issues`)
- What HTTP methods are needed? (GET list, GET detail, POST create, PATCH update, DELETE)
- Is pagination server-side or client-side?
- What does the response envelope look like? (`{ data, meta }` or flat array?)
- Are there any special query parameters? (search, sort, order, date ranges)

### 7. Codebase Reuse

**Ask when:** the feature touches concerns that may already exist in the codebase.

**Questions:**

- Should typed fields (severity, state) reuse existing domain types or create new ones?
- Are there existing utilities to reuse? (date formatting, duration calculation, status badge mapping)
- Should the DI module share cross-module dependencies, or be fully self-contained?
- Are there existing UI components to reuse for table columns? (Badge variants, Button styles, DataTable columns)

---

## Detection Heuristics

Use these heuristics to decide which question categories to include in the batch:

| If the user mentions... | Ask about... |
|---|---|
| "table", "list", "data table", "columns" | Column Display, Action Buttons, Filters |
| "status", "state", "workflow", "lifecycle" | State Machine |
| "severity", "priority", "type", "category" | Enum Values |
| "filter", "search", "filter bar" | Filter Behavior |
| "API", "endpoint", "backend", "fetch" | API Surface |
| A feature name that already exists in the codebase | Codebase Reuse |

If none of these keywords appear and the feature is small (e.g., a single utility function), skip Phase 0 entirely and go straight to Phase 1.

---

## Batch Presentation Format

Present all applicable questions in one message, grouped by category:

```
Before I generate specs, I need a few clarifications:

**State Machine** (required)
1. What states can [entity] be in?
2. What transitions are allowed?

**Action Buttons** (required)
3. What row actions exist, and are any conditional?

**Column Display**
4. How should [date column] be formatted — relative or absolute?

**Filter Behavior**
5. Are multiple filters AND or OR?
```

Never ask follow-up questions one at a time. Never proceed to Phase 1 without resolving 🔴 Blockers.
