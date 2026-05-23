# 📚 Book Finder

A warm, bookshop-inspired web app for searching books and managing your personal reading list — no account required.

---

## What It Does

- Search any book title or author via the [OpenLibrary API](https://openlibrary.org/developers/api)
- Browse the top 10 results with cover images, titles, and authors
- Save books to your personal reading list with one click
- Toggle each book between **Want to Read** and **Read**
- Your list persists across page refreshes — powered by Firebase Firestore

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Database | Firebase Firestore |
| Auth | Firebase Anonymous Auth |
| Book Search | OpenLibrary API (free, no API key) |
| Hosting | GitHub Pages |

---

## Project Structure

```
/
├── index.html       # App shell and layout
├── style.css        # Design system and component styles
├── firebase.js      # Firebase initialisation and Firestore helpers
└── app.js           # Search logic, save/toggle/remove, reading list rendering
```

---

## Design System

- **Vibe:** Warm, old-world bookshop
- **Fonts:** EB Garamond (headings) + system-ui (body)
- **Palette:** Warm creams, earthy browns, saddle-brown accent (`#8B4513`)
- **Cards:** Drop shadows, thin decorative borders, paper-texture background
- **Buttons:** Slightly rounded corners (`6px`), brown-on-cream

---

## Firebase Setup

1. Create a project at [console.firebase.google.com](https://console.firebase.google.com)
2. Enable **Firestore Database** (Production mode, `europe-west1` region)
3. Enable **Anonymous Authentication**
4. Register a Web App and copy your `firebaseConfig` into `firebase.js`
5. Set the following Firestore security rules:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/books/{bookId} {
      allow read, write: if request.auth.uid == userId;
    }
  }
}
```

---

## Data Model

Each saved book is stored as a document under the authenticated user's subcollection:

```
/users/{uid}/books/{openLibraryKey}
  ├── title       string
  ├── author      string
  ├── coverId     number
  ├── status      "want_to_read" | "read"
  └── savedAt     timestamp
```

The OpenLibrary key (e.g. `/works/OL45804W`) is used as the document ID, which automatically prevents duplicate saves.

---

## Deploying to GitHub Pages

1. Create a GitHub repository and push all files
2. Go to **Settings → Pages**
3. Set source to **main branch / root**
4. Your app will be live at `https://<your-username>.github.io/<repo-name>`

> **Note:** Your `firebaseConfig` will be visible in the source. This is safe as long as your Firestore security rules are correctly configured (see above).

---

## How Anonymous Auth Works

- Firebase assigns a unique ID to each browser session automatically
- The reading list persists as long as browser storage isn't cleared
- Opening the app in a different browser or incognito tab starts a fresh list
- No sign-up, no password, no email required

---

## Key Behaviours

- **Duplicate prevention:** Saving a book already in your list shows an "Already saved" message — no duplicates are written
- **Missing covers:** Books without a cover image show a warm placeholder
- **Results cap:** The OpenLibrary search is capped at 10 results for usability
