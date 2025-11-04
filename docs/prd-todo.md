# Product Requirements Document (PRD) - TODO App Upgrade (Due Dates, Priority, Filters)

## 1. Overview

We are upgrading the existing simple TODO app (currently supporting title and completion state) to improve task organization and focus. The enhancement introduces optional due dates, task prioritization (P1–P3), and filtering by temporal relevance (All, Today, Overdue) while keeping implementation lean (local-only storage, no backend changes). This PRD scopes what will be delivered in the MVP and what is deferred to Post-MVP to avoid over-extension and preserve teaching clarity for the bootcamp.

Assumptions:
- Existing baseline features (create/edit/delete tasks; mark complete; list tasks) remain unchanged and available.
- The frontend persists tasks in local storage only (no multi-user or server sync).

Goals:
- Provide lightweight urgency signaling via due dates and priorities.
- Allow users to focus on tasks needing attention today or overdue.
- Maintain simplicity for instructional use.

---

## 2. MVP Scope

In-scope for the first release (teachable, minimal complexity):
- Data Model Fields:
  - title (string, required)
  - description (string, optional – existing capability retained)
  - completed (boolean, existing)
  - dueDate (string, optional, ISO `YYYY-MM-DD`; invalid entries ignored gracefully)
  - priority (enum: `P1 | P2 | P3`, default `P3`)
- Priority Defaulting: If not specified, assign `P3`.
- Filters / Views (tab or segmented control UI):
  - All: shows all tasks (completed + incomplete)
  - Today: shows incomplete tasks with `dueDate === today`
  - Overdue: shows incomplete tasks with `dueDate < today`
- Local Persistence: Store tasks client-side (e.g., `localStorage`); no backend/API or external storage.
- Basic Styling for Priority: Color-coded badges (Red = P1, Orange = P2, Gray = P3).
- Validation Rules:
  - Title must be non-empty after trim.
  - Due date must match ISO pattern and be a valid calendar date; else treat as absent.
  - Priority must be one of the enum values; else fallback to default.
- Editing Flow: Users can update title, description, dueDate, priority.
- Filtering Logic: Filters recompute dynamically on data change (add/edit/toggle complete).
- Accessibility (minimal): Standard HTML semantics only (no enhanced keyboard nav work beyond browser defaults).

Non-Functional (MVP):
- Performance acceptable for < 500 tasks in local storage.
- No network dependency.
- Clear separation of task model logic from rendering (for future extensibility).

---

## 3. Post-MVP Scope

Enhancements planned for a subsequent iteration (not delivered in MVP):
- Advanced Sorting Order in task list:
  - Overdue tasks first
  - Then by priority (P1 → P2 → P3)
  - Then by due date ascending (earliest first)
  - Tasks without due date last
- Visual Overdue Highlighting: Overdue tasks visually emphasized (e.g., red border or background tint distinct from priority badge).
- More granular date filters (e.g., Upcoming / This Week).
- Additional priority visualization refinements (tooltips or legend component).
- Improved accessibility (keyboard shortcuts for filter switching, focus management patterns).
- Unit tests specifically targeting priority + filter interplay edge cases (beyond core MVP tests).

---

## 4. Out of Scope

Explicitly excluded from current and Post-MVP planning (requires separate prioritization before inclusion):
- Notifications (email, push, toast reminders)
- Recurring tasks / repetition rules
- Multi-user / authentication / shared lists
- External or cloud storage, syncing, or backend API integration
- Keyboard navigation enhancements beyond browser default interactions (for MVP scope specifically)
- Mobile-native packaging (PWA features, offline sync advanced logic)
- Subtasks, attachments, comments

---

## 5. Success Metrics

- Adoption: Users utilize non-default priorities (≥ 40% of tasks not P3) within sample usage.
- Focus Views: At least 50% of task interactions (toggle/edit) occur from Today or Overdue filters in demos.
- Data Integrity: Invalid dates never crash UI; silently treated as absent (0 uncaught exceptions on date parsing in test suite).

---

## 6. Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Ambiguity in date parsing | Incorrect overdue classification | Centralize date validation utility with tests |
| Over-complex sorting added early | Slows instructional pace | Defer to Post-MVP as documented |
| Priority colors conflict with overdue highlight | User confusion | Distinct visual treatment (badge vs background) |
| Local storage quota / corruption | Loss of tasks | Provide defensive JSON parse with fallback empty array |

---

## 7. Open Questions

- Should completed tasks disappear automatically from Today/Overdue upon completion (current assumption: yes — they are hidden)?
- Should we allow clearing a due date after it is set (assumed yes via edit form)?
- Do we need a bulk complete or bulk delete action in near future? (Not currently scoped.)

---

## 8. Acceptance Criteria (MVP)

1. Given I create a task with only title, then it’s saved with default `priority = P3` and no `dueDate`.
2. Given I set a valid ISO due date and it equals today, it appears in Today filter.
3. Given a task’s due date is before today and it is incomplete, it appears in Overdue filter.
4. Given a task is completed, it no longer appears in Today or Overdue filters but remains visible in All.
5. Given I enter an invalid date (e.g., `2025-13-40`), the task saves without a due date and no error toast is shown.
6. Priority badge colors follow spec (Red=P1, Orange=P2, Gray=P3) and are visible in All view.
7. Local storage retains tasks across page reloads.

---

## 9. Data Model (JSON Example)

```json
{
  "id": "uuid-1234",
  "title": "Prepare demo",
  "description": "Outline talking points",
  "completed": false,
  "priority": "P2",
  "dueDate": "2025-11-05" // optional
}
```

---

## 10. Engineering Notes

- Central utility: `parseDueDate(dateString)` returns `Date | null`.
- Filtering helpers: `isToday(task)`, `isOverdue(task)`.
- Local storage key: `todoApp.tasks.v2` (version bump for new fields).
- Migration: Existing tasks without priority get default assigned at load.

---

## 11. Test Coverage Outline (MVP)

- Model: Default priority assignment; invalid date ignored.
- Filtering: Today vs Overdue logic boundaries (midnight edge case).
- Persistence: Load/save cycle retains new fields.
- UI Rendering: Priority badge color mapping.

---

## 12. Non-Goals (Restatement)

Reiterating exclusions: no notifications, recurrence, backend, multi-user, advanced sorting (deferred), and accessibility enhancements.

---

Document finalized per meeting (Sept 16) and Slack confirmation (Sept 17).