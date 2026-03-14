# Cuppel Changelog

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
