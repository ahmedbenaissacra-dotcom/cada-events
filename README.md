# CADA Events PWA

A collaborative event planning Progressive Web App for two people, built with vanilla JavaScript and Firebase Firestore. Plan outings, trips, cultural activities, and more — with real-time sync and full offline support.

## Features

- **Real-time sync** — changes appear instantly on both devices via Firebase Firestore
- **Offline support** — works without internet; syncs when back online
- **Three views** — List, Timeline (by year/month), and Kanban (by theme)
- **Event theming** — 7 categories: Sortie/Balade, Culture, Famille, Gastronomie, Voyage, Sport/Jeux, Autre
- **Two-user mode** — toggle between Ahmed and Chloé; each event shows its creator
- **Lock screen** — password-protected access
- **Installable** — add to home screen on Android/iOS as a standalone app
- **Zero build step** — plain HTML/CSS/JS, no npm or bundler required

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla JavaScript, CSS Grid/Flexbox |
| Database | Firebase Firestore (real-time, offline-capable) |
| Offline cache | Service Worker (`cada-events-v2`) + localStorage fallback |
| Fonts | Google Fonts — Playfair Display + Inter |
| Hosting | GitHub Pages (or any static host) |
| PWA | Web App Manifest + Service Worker |

## File Structure

```
cada-events-pwa/
├── index.html        # Entire application (HTML + CSS + JS in one file)
├── manifest.json     # PWA metadata (name, icons, theme color)
├── sw.js             # Service Worker (offline caching)
├── DEPLOY.md         # Step-by-step deployment guide
└── icons/
    ├── icon-192.png  # Home screen icon
    └── icon-512.png  # Splash screen icon
```

## Quick Start

### 1. Set up Firebase

1. Go to [Firebase Console](https://console.firebase.google.com) and create a project.
2. Enable **Firestore Database** in test mode.
3. Register a **Web App** and copy the config object.

### 2. Configure the app

Open `index.html` and replace the placeholder Firebase config (around line 856):

```js
const firebaseConfig = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT.firebaseapp.com",
  projectId:         "YOUR_PROJECT_ID",
  storageBucket:     "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId:             "YOUR_APP_ID"
};
```

### 3. Deploy

Upload all files to any static host. For GitHub Pages:

```
git init
git add .
git commit -m "Initial deploy"
git remote add origin https://github.com/YOUR_USERNAME/cada-events-pwa.git
git push -u origin main
```

Then enable Pages in the repository settings (source: `main` branch, root `/`).

> See [DEPLOY.md](DEPLOY.md) for the full step-by-step guide including Firestore security rules.

## Views

### List View
Default view showing all events as cards in a responsive grid (1–3 columns). Filter by theme, add new events via the collapsible form, and see aggregate stats at the top.

### Timeline View
Year-by-year calendar layout. Navigate years with arrow buttons. Events are grouped by month; undated events appear in a separate section visible across all years.

### Kanban View
Horizontal scrolling board with one column per theme. Events within each column are sorted by date.

## Data Model

Events are stored in Firestore under the `ideas` collection:

```js
{
  title:     string,       // required
  theme:     string,       // 'sortie' | 'culture' | 'famille' | 'gastronomie' | 'voyage' | 'sport' | 'autre'
  who:       string,       // 'ahmed' | 'chloe'
  date:      string,       // 'YYYY-MM-DD' (optional)
  url:       string,       // optional link
  note:      string,       // optional comment
  createdAt: timestamp,
  updatedAt: timestamp
}
```

## PWA Installation

**Android (Chrome):** Open the app URL → tap the browser menu → "Add to Home Screen."

**iOS (Safari):** Open the app URL → tap the share icon → "Add to Home Screen."

Once installed, the app runs in standalone mode (no browser chrome) and loads instantly from cache.

## Offline Behavior

The Service Worker uses a split caching strategy:

- **Local assets** (HTML, manifest) — cache-first; loaded instantly, updated in background.
- **Firebase / Google Fonts** — network-first; falls back to cache when offline.

When Firestore is unreachable, all data is read from and written to `localStorage`. Changes sync to Firestore automatically when connectivity is restored.

## Security Notes

- The lock screen uses a base64-encoded password (`cada123` → `Y2FkYTEyMw==`). This is obfuscation, not encryption — treat it as a convenience lock.
- Firestore starts in 30-day test mode (open read/write). For long-term use, update the security rules to a permanent allow-all policy as described in [DEPLOY.md](DEPLOY.md).
- There is no user authentication. Anyone who knows your Firebase project ID can access the data. Keep the project private and the config out of public repositories, or add Firestore rules to restrict access.

## Customization

| What | Where |
|---|---|
| App password | `LOCK_HASH` constant in `index.html` (replace with `btoa('your-password')`) |
| User names | Search for `ahmed` / `chloe` in `index.html` |
| Theme labels & colors | `THEMES` object and CSS variables in `index.html` |
| Brand colors | `:root` CSS variables at the top of the `<style>` block |
| PWA name & colors | `manifest.json` |

## License

Personal project — no license applied.
