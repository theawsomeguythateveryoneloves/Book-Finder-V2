# AGENTS.md

Instructions for AI coding agents (Claude Code, Copilot, Cursor, etc.) working on this project.

---

## Project Overview

Book Finder is a plain HTML/CSS/JavaScript web app. There is no build step, no bundler, no npm, and no framework. Everything runs directly in the browser. Do not introduce build tooling unless explicitly asked.

---

## File Responsibilities

| File | Purpose |
|---|---|
| `index.html` | App structure only — no inline styles, no inline scripts |
| `style.css` | All visual styling — use CSS variables defined in `:root` |
| `firebase.js` | Firebase init, anonymous auth sign-in, all Firestore read/write helpers |
| `app.js` | UI logic — search, render results, handle user interactions, call firebase.js helpers |

Keep concerns separated. `app.js` should never import Firebase directly — it calls functions exported from `firebase.js`.

---

## Firebase Rules

- The Firestore path for all book documents is `/users/{uid}/books/{openLibraryKey}`
- The `uid` comes from `firebase.auth().currentUser.uid` after anonymous sign-in
- The `openLibraryKey` (e.g. `/works/OL45804W`) is used as the Firestore document ID
- Never write to a path that doesn't match this structure
- Do not add Firebase Storage, Cloud Functions, or any other Firebase products

---

## OpenLibrary API

- Search endpoint: `https://openlibrary.org/search.json?q={query}&limit=10`
- Cover image URL: `https://covers.openlibrary.org/b/id/{cover_id}-M.jpg`
- If `cover_edition_key` or `cover_i` is missing, show the placeholder cover
- Never add an API key — OpenLibrary is free and open
- Never increase the result limit above 10

---

## Design System — Do Not Break These

All colours and spacing are defined as CSS variables in `:root` in `style.css`. Do not hardcode colour values anywhere. The variables are:

```css
--color-bg          /* #F5F0E8 — warm cream page background */
--color-surface     /* #FDFAF4 — card background */
--color-text        /* #2C1810 — primary text */
--color-text-muted  /* #6B4F3A — secondary text (authors, meta) */
--color-accent      /* #8B4513 — buttons, links */
--color-accent-dark /* #6B3410 — hover state */
--color-border      /* #D4B896 — card and input borders */
--color-shadow      /* rgba(44, 24, 16, 0.12) — drop shadows */

--font-serif        /* 'EB Garamond', Georgia, serif */
--font-sans         /* system-ui, sans-serif */

--radius-btn        /* 6px */
--radius-card       /* 8px */
```

Headings always use `--font-serif`. Body text, buttons, and labels always use `--font-sans`.

---

## Duplicate Prevention Logic

Before writing a book to Firestore, check whether a document with that book's OpenLibrary key already exists for the current user. If it does, display an "Already saved" message near the Save button and abort the write. Do not use `setDoc` with merge — use an explicit existence check so the user gets feedback.

---

## Status Toggle

Each saved book has a `status` field: `"want_to_read"` or `"read"`. Toggling updates only this field via `updateDoc`. Do not rewrite the entire document on toggle.

---

## What Not To Do

- Do not add a login/signup UI — anonymous auth is silent and automatic
- Do not add pagination or infinite scroll — 10 results is the cap
- Do not add localStorage or sessionStorage — Firestore is the only persistence layer
- Do not use `innerHTML` to inject user-supplied content — use `textContent` and DOM methods
- Do not add CSS frameworks (Bootstrap, Tailwind, etc.) — all styles are hand-written
- Do not add JavaScript frameworks (React, Vue, etc.) — vanilla JS only
- Do not modify the Firestore security rules without noting the change clearly in a comment

---

## Testing Checklist

Before marking any task complete, verify:

- [ ] Search returns up to 10 results with cover images (or placeholder if none)
- [ ] Saving a new book adds it to the reading list immediately
- [ ] Saving a duplicate shows "Already saved" and does not write to Firestore
- [ ] Toggling status updates the UI and Firestore correctly
- [ ] Removing a book deletes it from Firestore and removes it from the UI
- [ ] Refreshing the page restores the full reading list
- [ ] The app works without errors in the browser console
- [ ] No hardcoded colour values exist outside of CSS variables
