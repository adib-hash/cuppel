# Cuppel Changelog

## v3.3.0 — 2026-03-28

### Todo Filter Fix, Project Canvas, Todo Templates, Calendar Export

**Todo filter tab layout fix:**
- Fixed "Adib" and "Ummey" stacking inside a single pill. The `#todo-member-tabs` span wrapper now uses `display: contents`, making the member tab buttons direct flex children of the tab bar — they appear side-by-side with "All" at equal size.

**Project canvas:**
- Each project detail now opens with a free-form text canvas above the checklist and files.
- Canvas text auto-saves to Firebase with an 800ms debounce — no save button needed.
- Live presence indicator: if your partner has the same project open, a pulsing dot and their name appear above the canvas ("Ummey is here").
- Presence is cleaned up automatically on modal close and on browser/tab disconnect via `onDisconnect().remove()`.
- Root listener update guard prevents the canvas textarea from being overwritten while the user is actively typing.

**Todo templates:**
- New "Templates" button on the Todo page header.
- Save the current active (not-done) task list as a named template.
- Templates modal shows all saved templates with task count; "Use" loads all template tasks into the current list; "Delete" removes the template.
- Template list auto-refreshes when the Firebase root listener fires (same pattern as other detail panels).

**Calendar export (.ics):**
- New "Export .ics to add both calendars after saving" checkbox in the Add Event modal — when checked, a `.ics` file downloads immediately after the event is saved.
- New "Export .ics" button in the Event Detail footer — downloads a `.ics` for any existing event.
- Generated file is RFC 5545-compliant with both household members as ATTENDEE entries (RSVP=TRUE), so opening it in Apple Calendar, Google Calendar, or Outlook prompts both people to accept. No backend or API keys required.

## v3.2.0 — 2026-03-28

### To-Do UX Overhaul

**Categories removed:**
- Removed the Category field from Add Task and Edit Task modals entirely
- Removed the Chores and Shopping filter tabs — filter bar is now clean: All / [You] / [Partner]
- Removed the category tag from todo rows and the task detail checklist view
- Cleaned up the mobile CSS rule that was hiding `.tag.tag-dim` on small screens (no longer needed)

**Modal scroll lock — depth-counter fix:**
- Replaced the boolean `_lockScroll`/`_unlockScroll` pair with a reference-counted version (`_modalDepth`)
- The body only gets physically locked/unlocked at the 0↔1 boundary — rapid modal transitions (e.g. close Day Sheet → open Event Detail) no longer cause a scroll-position jump because the body stays fixed throughout the transition
- The backdrop-click handler now calls `closeModal(m.id)` instead of directly manipulating DOM + calling `_unlockScroll()`, so it correctly participates in the counter

**Buttery modal scrolling:**
- Added `-webkit-overflow-scrolling: touch` to `.modal` for native momentum scrolling on iOS inside tall modals
- Added `overscroll-behavior: contain` to `.modal` to prevent scroll from chaining through the modal to the background page

**Swipe gesture reliability:**
- Added `touch-action: pan-y` to `#todo-render` — explicitly tells the browser that vertical touches on the list are for scrolling, not swipe. This removes the ambiguous startup lag and makes both scroll and swipe feel more intentional and immediate.

## v3.1.0 — 2026-03-28

### Multi-Day Events + Project File Attachments

**Multi-day calendar events:**
- Events now support an optional End Date field. A trip from Jan 1–3 shows dots on all three calendar days, not just the first.
- The "Start Date" label replaces "Date" in both the Add and Edit event modals. An "End Date (optional)" field appears below it.
- The calendar grid builds date-coverage sets for each event, so every day in a multi-day range gets a colored dot (matching event type).
- The Day Sheet modal now includes any event whose range overlaps the selected day — not just events starting on that day.
- Event Detail shows a compact date range ("January 1 – January 3, 2026") when an end date is set. Single-day events display as before.
- The upcoming events list shows "Jan 1 – 3" or "Jan 31 – Feb 2" for range events.
- End date is validated client-side: must be on or after the start date.

**Project file attachments:**
- Files can now be attached to any project — receipts, PDFs, photos, or any document.
- A "Files" section appears in the project detail modal below the task checklist.
- "+ Upload" button opens the native file picker. Files upload to Firebase Storage under `/households/{id}/projects/{id}/`.
- Per-file limit: 10 MB (validated client-side and enforced in `storage.rules`). Per-project limit: 10 files.
- Each file row shows a type-appropriate icon (image / PDF / generic), filename, size, and a download link. Clicking the filename opens the file in a new tab.
- Deleting a file removes both the Storage object and the metadata from Realtime Database.
- Deleting a project also cleans up all its Storage files.
- Firebase Storage compat SDK (v9.23.0) added. `storage.rules` file added for deploy reference.

**One-time Firebase setup required:**
1. Firebase Console → Storage → Rules → paste contents of `storage.rules`

## v3.0.0 — 2026-03-27

### Google Auth + Household Security

**Authentication — Google Sign-In:**
- Full Google OAuth login via Firebase Auth. The app now requires a Google account before any data is visible.
- Auth screen with lock icon, Cuppel branding, and "Continue with Google" button (Google G logo SVG — no emojis).
- Sign-out from Settings page with confirmation dialog.

**Household model — two-person private space:**
- First-time users choose: Create a new household, or Join an existing one with an invite code.
- Creating a household generates an 8-character random hex invite code (4 billion combinations), expires in 48 hours, single-use.
- The waiting screen displays the code + a "Copy invite link" button. The link auto-fills the code for the joining partner via `?join=CODE` URL param.
- Once two members are in a household, the app opens automatically with no further action needed.

**Firebase Security Rules (`database.rules.json`):**
- All data moved from `/cuppel/` to `/households/{householdId}/`. New households start empty.
- `/users/$uid` — only the matching authenticated user can read/write their own profile.
- `/households/$hid` — read/write requires your UID to exist in `members`. Strangers with the URL see nothing.
- `/households/$hid/members/$uid` — each user can only add themselves, and only once (`!data.exists()` guard).
- `/inviteCodes/$code` — any authenticated user can read a code (needed to validate before joining); only the creator can write it. Codes are deleted on use.

**Dynamic identity — no more hardcoded names:**
- The `who` field on todos now stores the user's Firebase UID (not 'adib'/'ummey').
- Filter tabs, todo labels, streak chips, greeting, and the "yours" badge all derive names from `S.members[uid].displayName`.
- Todo filter tabs for household members are rendered dynamically after login — first names from Google profiles.
- Add/Edit Task "Assign to" selects are populated dynamically with real first names.

**Settings page updated:**
- Shows both household members (name, email, avatar initials) with "You" / "Partner" labels.
- "Household" row shows both members' first names (e.g. "Adib & Ummey").
- Account section with Sign Out button.
- Developer Tools: "Clear All Data" replaces the old "Reset to Seed Data" (seed data removed).

**One-time Firebase Console setup (user must do):**
1. Firebase Console → Authentication → Sign-in method → Enable Google
2. Authorized domains → add `cuppel.ihsan.build`
3. Realtime Database → Rules → paste contents of `database.rules.json`

## v2.1.0 — 2026-03-27

### Clean & Sharpen

**Removed: Finances section**
- Removed the Finances page, Add/Edit modals, sidebar nav item, and More page tile entirely. Finance data was useful but didn't drive daily shared behavior or decision-making — removing it simplifies navigation meaningfully.
- All finance CSS (~46 lines), JS (~150 lines including 8 functions), HTML (page section, 2 modals, sidebar item), and Firebase state references cleaned up.

**Home: Countdown card (replaces Monthly Spend)**
- The Monthly Spend quick card is replaced with a "Countdown" card showing days until the next birthday or anniversary from the Calendar. Finds the next annual occurrence (this year or next if already passed) and displays the event name + days remaining. Links to Calendar. Falls back to "No birthdays or anniversaries" if none exist.

**Home: Shared Quick Note**
- A new "Quick Note" card at the bottom of the Home page. One shared textarea synced to `ROOT.child('quickNote')` in real time — both partners see each other's changes live via a dedicated Firebase listener. Auto-saves 800ms after typing stops, shows a brief "Saved" indicator. Character counter (max 500). iOS-safe font-size (16px, no auto-zoom). The input respects focus state so typing is never overwritten by incoming syncs.

**Home: Check-in freshness prompt**
- If no weekly check-in has been submitted in 7+ days (or ever), a small amber "Weekly check-in — last done X days ago" chip appears in the Home hero below the streak row. Tapping navigates directly to Inspire. Hidden when up-to-date.

## v2.0.0 — 2026-03-27

### UX & Reliability Sprint

**Modal scroll lock fix (CLAUDE.md compliance):**
- `openModal`/`closeModal`/backdrop click handler all switched from `document.body.style.overflow = 'hidden'` to the `position: fixed` pattern. This prevents the iOS page-jump-to-top bug that `overflow: hidden` causes. A `_lockScroll()`/`_unlockScroll()` helper pair handles save/restore of scroll position.

**Undo on all destructive actions:**
- `delTodo`, `delEvent`, `delFin`, `delProj`, `delJar` all now show a toast with an Undo button. The deleted item's data is snapshotted before removal; tapping Undo re-sets it to Firebase. `delProj` also now correctly resets `_detailProjId = null` after deletion (it was missing before, leaving stale state).

**Optimistic UI on todo toggle:**
- `toggleTodo()` now immediately updates the DOM (`done` class, checkmark appearance) before the Firebase round-trip. The Firebase `.on('value')` listener still confirms and re-renders on sync — the optimistic update just eliminates the visible lag.

**Offline / connection indicator:**
- Firebase `.info/connected` listener added. When the app loses connectivity, a slim bar appears below the top nav after a 2-second grace period (prevents flash on normal page load). Bar disappears immediately when connection is restored.

**Check-in history — show all:**
- Removed the `.slice(0, 4)` cap that limited the history view to the 4 most recent check-ins. All check-ins are now shown. Title updated to "Check-in History (N)" with the count.

**Swipe gestures on todo list:**
- Touch event delegation on `#todo-render`. Swipe right → complete the task (calls `toggleTodo`). Swipe left → delete with undo (calls `delTodo` which shows the toast). Threshold: 80px. Directional lock after 8px movement prevents conflicts with vertical scrolling. Visual feedback via `swiping-right` / `swiping-left` background color classes during swipe.

## v1.9.0 — 2026-03-16

### Bug Fixes, Features & Navigation

**Critical bug fixes:**
- **Dead `renderScrap` reference removed**: `scrapbook:renderScrap` was left in the `renders` object in `nav()` after the Scrapbook feature was reverted. Removed to prevent runtime errors.
- **`doneAt` now cleared on un-check**: When a todo is unchecked, `doneAt` is explicitly set to `null` in the Firebase update inside `toggleTodo()`. Previously it was never cleared, causing incorrect streak calculations.
- **Wiki tile values are now copyable**: Added `user-select: text; -webkit-user-select: text` to `.wiki-tile-val` so revealed values can be selected and copied on iOS and all other platforms. The `.blurred` state still uses `user-select: none`.

**Navigation fix:**
- **"More" tab replaces "Inspire" in bottom nav**: Wiki, Finances, Inspire, and Settings were unreachable on mobile without a sidebar. The 5th bottom nav slot is now a "More" tab that navigates to a new `page-more` page. The More page shows a 2-column grid of large navigation tiles (Inspire, UsWiki, Finances, Settings) with icon, name, and description. Sidebar on desktop still navigates directly to Inspire.

**New features:**
- **Project checklist items now editable**: In `renderProjDetail()`, the static `<span class="proj-ci-text">` is replaced with an `<input class="proj-ci-edit-input">`. Edits save on blur or Enter via the new `saveProjItem(itemId, text)` function which calls Firebase `.update({text})`.
- **Calendar grid shows colored event dots**: The single CSS `::after` dot is replaced with dynamically rendered dots (up to 3) per day cell. Each dot is colored to match the event type (rose for date, gold for birthday, purple for anniversary, sage for appointment, blue for social). Rendered as a `.cal-dots` flex row below the day number.
- **Nudge smart re-trigger**: `dismissNudge()` now stores a timestamp (`cuppel_nudge_dismissed_at`) instead of a boolean flag. In `renderHome()`, after Firebase data loads, the app checks: if it's been 14+ days since the last `date`-type event (or no date events exist), the dismissal is cleared and the nudge bar is shown again. The init script is updated to read the new key.
- **Finances empty state**: When `S.finances` is empty, `renderFin()` now shows a proper empty state (icon + "No expenses yet. Tap + Add to track your first one.") inside `fin-list`. The hero summary still renders with $0 values.
- **"Clear all completed" confirmation**: `clearCompleted()` now shows a confirmation dialog ("Clear all N completed tasks?") before deleting. Cancelling aborts the operation.

## v1.8.0 — 2026-03-16

### Bug Fixes, Feature Completions & UX Polish

**Critical bug fixes:**
- **Streak row now populated**: `renderHome()` now computes and renders streak chips for Adib and Ummey. Streak = consecutive days (up to today) where at least one task was marked done. `toggleTodo()` now stores a `doneAt` date string when a task is completed. Chips show "Adib · N day streak" and "Ummey · N day streak" with matching accent pip colors.
- **"Next Up" no longer stuck on "Loading…"**: When `S.events` is empty or has no future events, the card now falls back to "No upcoming events / Tap to add one" instead of remaining permanently on the loading placeholder.
- **Nudge dismissal now persists**: Tapping × stores `cuppel_nudge_dismissed` in `localStorage`. The nudge does not reappear on reload. Init script restores the dismissed state immediately on load.
- **Projects now reachable on mobile**: Bottom nav "Wiki" slot replaced with "Projects" (matching icon). Wiki remains accessible from the desktop sidebar. Settings was already reachable via the gear icon in the top nav.

**High-impact feature gaps:**
- **Expense editing**: Every finance row now has an Edit (pencil) button alongside the Delete button. Tapping it opens a pre-populated "Edit Expense" modal with all fields (name, amount, frequency, category, paid by). `saveEditFin()` updates via Firebase `.update()`.
- **Completed todos collapse by default**: The "Completed (N)" section is now collapsed on load. A chevron toggle expands/collapses it. When expanded, a "Clear all" button appears — deleting all completed tasks with an undo toast (3.5s window).
- **Toast wired up**: `showToast(msg, undoFn)` and `undoToast()` implemented. Used by "Clear all completed".

**Mobile polish:**
- **Jar chip actions always visible on mobile**: `.jar-chip-del` and `.jar-chip-try` now display as `flex` on screens ≤900px, matching the todo action button behavior.

**Wiki:**
- **"Reveal all" toggle**: New ghost button in the UsWiki header. Tapping "Reveal all" unblurs all sensitive tiles at once for the session; tapping "Hide all" re-blurs them. Per-tile tap-to-reveal still works independently.

## v1.7.0 — 2026-03-15

### Sprint 5 — Calendar: Interactive & Fully Functional

**Clickable date cells — Day Sheet:**
- Tapping any day in the monthly grid opens a Day Sheet modal showing all events for that day, sorted by time
- Each event row in the Day Sheet is clickable — opens the Event Detail modal
- "+ Add" button in the Day Sheet pre-fills the Add Event modal with that date and closes the sheet
- Empty state shown when a day has no events

**Event Detail modal:**
- Clicking any event (from the upcoming list or a Day Sheet) opens a full detail view
- Shows event name, type badge, date, time (formatted as 12-hour, if set), relative label ("Today", "Tomorrow", "In Xd", "Xd ago"), and notes block
- **Edit** button pre-fills the edit modal from the detail view
- **Delete** button removes the event from Firebase and closes the modal immediately

**Edit + Delete now fully wired:**
- `saveEditEvent()` implemented — updates name, date, type, time, and notes via Firebase `.update()`
- `delEvent()` implemented — removes event, closes modal, no confirmation dialog (consistent with app pattern)

**Time + Notes fields on events:**
- Optional `time` field (input type=time, stored as "HH:MM", displayed as "2:30 pm")
- Optional `notes` field (textarea)
- Added to both Add Event and Edit Event modals
- Displayed in Event Detail modal and in the upcoming list (time shown inline, e.g. "Mar 28, 2026 · 10:30 am")

**Upcoming list improvements:**
- Every item is now clickable — opens Event Detail
- Relative label chip next to each event: "Today" (gold), "Tomorrow"/"In Xd" (sage), "Xd ago" (dimmed)
- Past events shown at 45% opacity

**"Today" button:**
- Ghost button in the calendar header, visible only when viewing a non-current month
- Tapping it resets the view to the current month

**Seed data updated:**
- Dentist appointment now includes `time: "10:30"` and `notes` so the new fields are visible on first use

## v1.6.0 — 2026-03-14

### Sprint 4 — PWA / Add to Home Screen

- **`manifest.json`**: Web app manifest with name, short name, theme/background color (`#0D0F14`), standalone display mode, portrait orientation, and SVG icons (standard + maskable for Android)
- **`icon.svg`**: Cuppel brand icon — two interlocking gold/rose circles on the dark background, with rounded corners for standard display
- **`icon-maskable.svg`**: Same mark scaled into the safe zone for Android's circular/squircle crop
- **`sw.js`**: Minimal network-first service worker — caches the app shell on install, clears old caches on activate, serves fresh network responses and falls back to cache when offline
- **iOS meta tags**: `apple-mobile-web-app-capable`, `apple-mobile-web-app-status-bar-style: black-translucent`, `apple-mobile-web-app-title: Cuppel`, `apple-touch-icon` link — enables true standalone mode when added to the iPhone home screen
- **`theme-color`**: `#0D0F14` so the browser chrome matches the app background on Android
- Service worker is registered on `window load` with a silent catch — no errors in unsupported environments
- Projects full CRUD (Sprint 1) was already complete: card click opens detail modal, edit/delete project, add/check/delete checklist items, auto-calculated progress %

## v1.5.0 — 2026-03-14

### Sprint 3 — Jar "Tried It"

**Date Jar — mark ideas as tried:**
- Each jar chip now shows a sage checkmark button on hover (always visible on mobile): tap it to mark the idea as "tried it" with today's date
- A red X button lets you delete an idea entirely
- Tried ideas move out of the active chips and into a collapsible "Tried it (N)" section below — shown with strikethrough and the date tried
- The "Tried it" section is collapsed by default; tap the header to expand/collapse
- The home screen "Date Idea" shuffle now only picks from un-tried ideas
- Sprint 1 finance and todo work was already complete: `paid_by`/`category` fields save to Firebase, category breakdown renders on the Finances page, and due dates are fully wired up on todos (overdue/due-soon visual cues)

## v1.4.0 — 2026-03-14

### Sprint 2 — Wiki now fully editable

**Wiki (UsWiki) — moved from hardcoded to Firebase-backed:**
- All wiki data now lives in Firebase under a `wiki` node — changes sync between both people in real time
- **Add entry**: each section has a "+ Add" button that opens a modal for label, value, and a "Sensitive" toggle (blurred by default)
- **Edit entry**: each tile shows edit/delete action buttons (hover on desktop, always visible on mobile) — click the pencil to update label, value, or sensitivity setting
- **Delete entry**: trash icon per tile removes it from Firebase immediately
- **New section**: "+ New Section" button in the page header — pick a name and icon type (Document, Security, Home, Alert)
- **Delete section**: trash icon per section header — prompts for confirmation if the section has entries
- Blur-reveal behavior preserved — tapping a sensitive tile still toggles the blur
- Seed wiki data migrated to the new Firebase structure; Reset to Seed Data in Settings also resets wiki

## v1.3.0 — 2026-03-14

### Bug Fixes & Feature Completions (Sprint 1)

**Bug fixes:**
- **Calendar month navigation**: `shiftCal()` was called by prev/next buttons but never defined. Added `calOffset` state variable and working `shiftCal(n)` function — calendar now navigates correctly across months
- **Todo due dates**: `addTodo()` silently dropped the due date field. Now saves and persists `due` to Firebase. `editTodo()` populates the date picker, `saveEditTodo()` writes it back
- **Finance `paid_by` and `category`**: `addFinance()` ignored the Category and Paid By modal fields. Both now saved to Firebase. `renderFin()` shows tags below each item
- **Project cards not clickable**: `.proj-card` divs had no click handler. Added `openProjDetail()` — clicking a project card now opens the detail modal with full CRUD (edit, delete, add/check/delete checklist items, auto-calculated progress %)
- **Check-in responses lost on save**: Answers were captured in the UI but never written to Firebase. Added `submitCheckin()` which persists the week's responses. Check-in shows "submitted" state after completion; last 4 weeks of history shown below

**New features:**
- **Settings page**: Added via sidebar nav and top-nav gear icon. Shows app version (v1.3.0), couple name, and a "Reset to Seed Data" dev tool
- **Finance category breakdown**: Visual bar chart by category rendered below the expense list (only shown when categorized items exist)
- **Todo overdue styling**: Overdue todos now show a left red border and an "Overdue" badge; due-soon items show "In Xd" in gold; future dates shown in sage

## v1.2.0 — 2026-03-14

### Mobile UI & Typography Polish
- **Font size bumps throughout**: `nudge-desc` 12→13px, `nudge-title` 13→14px, `ev-name` 13→14px, `proj-desc` 13→14px, `sub-text` 13→14px, `inspire-name` 13→14px, `inspire-desc` 12→13px, `jar-chip` 12→13px, `checkin-q` 15→16px — all content text now comfortably readable at arm's length
- **Mobile todo text**: bumped from 15→16px on mobile
- **Fixed invisible edit/delete buttons on mobile**: `.todo-edit` and `.todo-del` were `opacity: 0`, only revealed on hover — completely untappable on touch. Now always visible on mobile.
- **Reduced todo row clutter on mobile**: category tag hidden on mobile to give the todo text, expand, who-tag, and action buttons room to breathe; row gap tightened from 13→10px
- **Replaced `▶` Unicode arrow** in todo expand button with a proper SVG chevron — consistent with no-emoji-adjacent-characters rule

## v1.1.0 — 2026-03-14

### Mobile & UI Polish
- **Replaced all emojis** with cohesive inline SVG icons throughout the app (navigation, page headers, content, action buttons, empty states)
- **Fixed iOS auto-zoom**: Added `maximum-scale=1` to viewport meta tag; all inputs/textareas at 16px font-size globally (not just via media query)
- **Fixed modal scroll lock**: Body scroll now locked (`overflow: hidden`) when any modal opens, restored on close — prevents background scroll conflict on mobile
- **Improved mobile typography**: Page titles scale to 26px on mobile, todo text to 15px, subtitles to 14px, check-in questions to 16px for better legibility
- **Increased bottom padding** on mobile main content to 24px above bottom nav (was 16px)
- **Finance icons**: Replaced emoji icons with elegant Playfair Display initials (e.g. "R" for Rent, "SD" for Spotify Duo) — removed emoji input field from Add Expense modal
- **Inspire tiles**: Replaced large emoji thumbnails with warm gradient color blocks and Playfair Display initial letters
- **Logo icon**: Replaced game controller emoji with two interlocking circles (representing a couple) in gold/rose
- **Streak chips**: Replaced emoji with small colored accent dots matching the app palette
- **Wiki sections**: Replaced emoji section headers with consistent SVG icons (document, shield, home, alert)
- **Event type dropdown**: Removed emojis from option labels

## v1.0.0 — Initial release
- Firebase Realtime Database backend
- Home dashboard with streaks, nudge, quick cards
- Shared To Do list with subitems and real-time sync
- Calendar with event management
- Finances tracker (recurring expenses)
- Projects with progress tracking
- UsWiki shared vault with blur-reveal
- Inspire page with date Jar, relationship check-in, date night ideas
- Mobile-first layout with bottom navigation
- Dark theme with gold/rose/sage accent palette
- Playfair Display + Sora typography
