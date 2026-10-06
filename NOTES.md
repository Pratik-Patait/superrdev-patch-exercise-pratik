# NOTES

## Summary of Changes

Four bugs were identified and patched across all three layers:

1. **SQL operator precedence (critical)** — The `WHERE` clause in `TaskRepository.java`, `search_tasks.sql`, and `task_search_package.sql` was missing parentheses around the `OR` condition joining title and description search. Because `AND` binds tighter than `OR`, archived tasks leaked into results and the status filter was silently bypassed for title matches. Fixed by wrapping the `OR` in explicit parentheses.

2. **Artificial `Thread.sleep()` in controller** — `TaskController.java` blocked the request thread for up to 1 second (inversely proportional to query length) under the guise of "complexity estimation." Removed the sleep; kept the log line.

3. **Loading state not cleared on error** — In `useTasks.js`, the `.catch()` handler never called `setLoading(false)`, so the UI permanently displayed "Loading tasks..." after any API failure. Added the missing call.

4. **Pagination not reset on filter change** — In `App.jsx`, changing the search query or status filter did not reset the page to 1, causing users to see empty results if the new result set had fewer pages. Added wrapper handlers that reset page state.

## What Was Left Unchanged

- **No debounce on search input** — a UX improvement, not a bug; outside patch scope.
- **`System.out.println` logging** — should be SLF4J in production, but changing logging infrastructure is a refactor.
- **H2 console enabled** — acceptable for a dev exercise; would be disabled in production via a profile.
- **No input validation or auth** — architectural concerns beyond a focused patch.

## Biggest Remaining Risk

The application has **no input validation, authentication, or authorization**. Any client can hit the API directly. Combined with the `@CrossOrigin` annotation and exposed H2 console, this is the largest attack surface.

## AI Tools Used

I used the Google Antigravity extension in VS Code as a pairing partner to scan the repository and draft initial fixes. Initially, the AI suggested over-engineered solutions like implementing a debounce hook for search and adding comprehensive error boundaries. I chose to discard those suggestions to adhere to the timebox and the 'focused patch' instructions. I did, however, accept its help in identifying the SQL parenthesis bug and generating the structural draft for this NOTES.md file, which I then manually edited for accuracy
