# Cuppel Changelog

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
