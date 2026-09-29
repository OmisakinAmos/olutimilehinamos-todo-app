# AGENTS.md

Instructions for any AI agent or developer working on this project. Read this before changing code.

## Project overview

A single-file to-do list app (`todo.html`) with task creation, viewing, editing, deletion, notes, due dates, priority, search and sorting. It runs in the browser with no build step and is published as a hosted page.

## Project structure

```
/
├── AGENTS.md    # this file
└── todo.html    # the whole app: HTML, CSS and JS in one file
```

Keep it as one file unless the owner asks otherwise.

## Coding rules

1. **Self-contained.** No external scripts, stylesheets, fonts, images or network requests. Everything is inline. Use system font stacks with fallbacks.
2. **Vanilla JavaScript only.** No frameworks, no build tools, no npm packages.
3. **Safe rendering.** Put user-entered text (title, notes) into the page with `textContent` or `.value`, never `innerHTML`. `innerHTML` is allowed only for strings the app builds itself from fixed values (dates, priority labels).
4. **Storage.** Persist tasks in `localStorage` under the key `todo-tasks`.
   - Wrap every read and write in `try/catch`.
   - The app must work with empty or unavailable storage.
   - Never rename the key or change the task shape without a migration (see the load step in `todo.html`).
5. **No `<form>` tags.** Use `click` and `keydown` handlers.
6. **State flow.** Change the `tasks` array, call `save()`, then call `render()`. Do not edit the DOM directly to reflect state.

## Data model

Each task has this shape. Any new field needs a default in the load step so older saved tasks still open.

```js
{ id: string, text: string, notes: string, due: "YYYY-MM-DD" | "",
  pri: "none" | "low" | "medium" | "high", done: boolean, created: number }
```

## Conventions

- **Naming:** `camelCase` for variables and functions, `kebab-case` for CSS classes and storage keys.
- **CSS:** define colors as custom properties on `:root`. Every color token needs a dark-mode value in both the `prefers-color-scheme` block and the `[data-theme="dark"]` block. Give `body` an explicit background.
- **Layout:** mobile first, content width capped at about 560px, no horizontal page scroll. Keep the safe-area inset padding on `:root`.
- **UI copy:** sentence case, plain verbs, buttons say what they do ("Save changes", "Delete task"). Empty states tell the person what to do next. Use the same word for the same action everywhere.
- **Confirmations:** deleting a task always asks first.
- **Comments:** only where the reason is not obvious from the code.
- **Scope:** make the smallest change that meets the request. Do not add features, dependencies or restyling unprompted.

## Accessibility requirements

- Every interactive control has a visible label or an `aria-label`.
- Keyboard focus is always visible, and every action works with the keyboard alone (Enter adds a task, Esc closes the details dialog).
- Text meets WCAG AA contrast in both light and dark mode.
- Overdue and priority are never shown by color alone. Text must say it too.

## Validation and testing requirements

There is no test runner, so validation is a manual pass plus a quick automated sanity check. Complete all of it before calling a change done.

**Automated checks (run in a browser console or a headless browser):**

- The page loads with zero console errors and zero failed network requests.
- No request leaves the page (search the file for `http://` and `https://`; the only allowed hits are none).

**Functional checklist:**

| Area | Must pass |
|---|---|
| Create | Add by button and by Enter. Blank or whitespace-only input adds nothing. |
| View | Tapping a task opens the details dialog with all fields filled in. |
| Edit | Changing title, notes, due date, priority and done, then saving, updates the list. Cancel discards changes. Saving an empty title is blocked. |
| Delete | Both delete paths ask for confirmation. Cancelling keeps the task. |
| Notes | Notes persist, show "has notes" in the list, and are searchable. |
| Due dates | A past date on an unfinished task reads "Overdue". A completed task never does. |
| Search, filter, sort | All three work together. Empty results show "No tasks match." |
| Clear completed | Removes only done tasks and hides itself when none exist. |
| Persistence | Reload the page and confirm tasks, notes, dates and priorities remain. |
| Migration | Load a task saved in the old shape (`{ text, done }` only) and confirm it opens and edits cleanly. |

**Edge cases:**

- Storage blocked or empty: the app still works for the session and shows the empty state.
- A 200-character title and very long unbroken words wrap without breaking layout.
- Text such as `<b>x</b>` or `<img src=x>` in a title or note shows as plain text and never runs.
- 100+ tasks stay responsive.

**Visual checks:**

- Light and dark mode, at 360px, 768px and desktop widths.
- No horizontal scroll at any width. Dialog fits a small phone screen.

## Publishing rules

- Publish `todo.html` as a hosted page. When updating, republish to the existing artifact link rather than creating a new one.
- Do not use `window.storage`, `window.claude` or the Anthropic API in this file. They do not work on a published page.
- Keep the file under 16 MB (it is currently a few tens of KB).

## Definition of done

1. The functional checklist and edge cases above pass.
2. No console errors, no external requests.
3. Existing saved tasks still load after the change.
4. The change is limited to what was requested.
5. The updated file is republished, and the reply says in one or two sentences what changed.
