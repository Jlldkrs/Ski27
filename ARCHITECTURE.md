# Ski27 — Architecture & Tech Stack

A collaborative group holiday planner built as a single-page web app.
This document explains how the app is built so that anyone — including beginner programmers — can understand, modify, and extend it.

---

## Table of Contents

1. [What Does This App Do?](#what-does-this-app-do)
2. [Tech Stack at a Glance](#tech-stack-at-a-glance)
3. [Project Structure](#project-structure)
4. [How the Pieces Fit Together](#how-the-pieces-fit-together)
5. [Frontend (What Users See)](#frontend-what-users-see)
6. [Database (Where Data Lives)](#database-where-data-lives)
7. [Authentication (Who Can Access What)](#authentication-who-can-access-what)
8. [Data Flow (How Everything Connects)](#data-flow-how-everything-connects)
9. [Key Concepts for Beginners](#key-concepts-for-beginners)
10. [Running the App Locally](#running-the-app-locally)

---

## What Does This App Do?

Ski27 helps a group of friends plan a holiday together. It lets everyone:

- **Track expenses** — Record who paid for what, split costs, and see who owes whom.
- **Plan a schedule** — Add activities with dates and times.
- **Manage a packing list** — Create items, assign them to members, and check them off.
- **View trip details** — Destination, dates, accommodation, and notes.
- **See personal stats** — Your share of expenses, your balance, and your packing progress.

Everything syncs in real time, so when one person adds an expense on their phone, everyone else sees it instantly.

---

## Tech Stack at a Glance

| Layer        | Technology              | What It Does                                      |
| ------------ | ----------------------- | ------------------------------------------------- |
| **Frontend** | HTML, CSS, JavaScript   | Everything the user sees and interacts with        |
| **Database** | Firebase Realtime DB    | Stores all trip data in the cloud                  |
| **Auth**     | Firebase Authentication | Lets the app connect to the database securely      |
| **Hosting**  | Any static file server  | Serves the single HTML file (no server needed)     |
| **Hashing**  | Web Crypto API          | Securely hashes passwords in the browser           |

**No build tools, no package manager, no frameworks.** The entire app is one HTML file with embedded CSS and JavaScript. You open it in a browser and it works.

---

## Project Structure

```
Ski27/
└── index.html        <-- The entire application (HTML + CSS + JS)
```

Yes, it really is just one file. Here is how that file is organized internally:

```
index.html
│
├─ <head>
│   ├─ Meta tags (viewport, mobile settings)
│   └─ <style> block — All CSS styles
│
├─ <body>
│   ├─ Login screen
│   ├─ Admin setup screen (shown on first use)
│   └─ Main app shell
│       ├─ Header (trip name, profile/settings buttons)
│       ├─ Content area (switched by tabs)
│       │   ├─ Overview tab
│       │   ├─ Expenses tab
│       │   ├─ Schedule tab
│       │   └─ Menu tab (packing list)
│       ├─ Sub-pages (profile, settings, etc.)
│       └─ Bottom tab bar (navigation)
│
└─ <script> blocks
    ├─ Firebase SDK (loaded from CDN)
    ├─ Firebase configuration
    ├─ Application state (`data` object)
    ├─ Firebase initialization & auth
    ├─ UI rendering functions
    └─ Event handlers & business logic
```

---

## How the Pieces Fit Together

Here is the big picture, simplified:

```
┌──────────────────────────────────────────────────┐
│                   User's Browser                 │
│                                                  │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐ │
│  │    HTML     │  │    CSS     │  │ JavaScript │ │
│  │ (structure) │  │  (styles)  │  │  (logic)   │ │
│  └────────────┘  └────────────┘  └─────┬──────┘ │
│                                        │        │
│                                        v        │
│                               ┌────────────────┐│
│                               │  Global `data`  ││
│                               │    object       ││
│                               └───────┬────────┘│
└───────────────────────────────────────┼──────────┘
                                        │
                              read/write (real-time)
                                        │
                                        v
                              ┌──────────────────┐
                              │ Firebase Realtime │
                              │     Database      │
                              │    (the cloud)    │
                              └──────────────────┘
```

1. The **HTML** defines the layout (buttons, forms, tabs).
2. The **CSS** makes it look good (colors, spacing, animations).
3. The **JavaScript** makes it interactive (add expenses, switch tabs, calculate balances).
4. The **`data` object** in JavaScript holds the current state of the trip.
5. **Firebase** stores that data in the cloud so it persists and syncs across devices.

---

## Frontend (What Users See)

### HTML — The Structure

The HTML uses a **tab-based layout**. Think of it like a phone app with tabs at the bottom. There are four main tabs:

| Tab        | What It Shows                              |
| ---------- | ------------------------------------------ |
| Overview   | Trip details, members, balances            |
| Expenses   | List of all expenses, totals               |
| Schedule   | Activities grouped by date                 |
| Menu       | Packing list organized by category         |

Switching tabs doesn't load a new page — it just shows/hides different `<div>` elements. This is what makes it a **Single-Page Application (SPA)**.

### CSS — The Look and Feel

The app uses a **dark theme** with CSS custom properties (variables) for consistent colors:

```css
:root {
    --bg: #0f172a;       /* Dark navy background      */
    --surface: #1e293b;  /* Card backgrounds           */
    --accent: #38bdf8;   /* Cyan blue for highlights   */
    --danger: #f87171;   /* Red for warnings/delete     */
    --success: #4ade80;  /* Green for positive amounts  */
    --text: #f1f5f9;     /* Main text color             */
}
```

> **Beginner tip:** CSS variables let you define a color once and reuse it everywhere. If you want to change the accent color, you only change it in one place.

The design is **mobile-first**, meaning it is built for phones first and works on larger screens too. It uses:

- `min-height: 100dvh` — fills the phone screen properly.
- Safe area insets — avoids the notch on modern phones.
- Large touch targets — buttons are easy to tap.

### JavaScript — The Behavior

All the app logic is plain JavaScript (no React, Vue, or other framework). Here are the main patterns:

**Global state object:**
```javascript
let data = {
    trip: { destination: '', start: '', end: '', ... },
    members: [],
    expenses: [],
    activities: [],
    packingItems: [],
    currency: 'EUR'
};
```

This single object holds everything about the trip. When you add an expense, it gets pushed into `data.expenses`. When you change the destination, `data.trip.destination` gets updated.

**Rendering:**
After any change, the app calls rendering functions like `renderAll()`, `renderExpenses()`, or `renderBalances()`. These functions read from the `data` object and update the HTML on the page.

```
User action (e.g. "Add Expense")
    → Update `data.expenses`
    → Call `save()` to push to Firebase
    → Call `renderExpenses()` to update the screen
```

**Tab switching:**
```javascript
function switchTab(tabName) {
    // Hide all tab content
    // Show the selected tab
    // Update the active state in the tab bar
}
```

---

## Database (Where Data Lives)

The app uses **Firebase Realtime Database**, a cloud database by Google. Here is what the data looks like:

```
Firebase
├── ski27/                          <-- All trip data
│   ├── trip/
│   │   ├── destination: "Chamonix"
│   │   ├── start: "2026-03-15"
│   │   ├── end: "2026-03-22"
│   │   ├── accommodation: "Alpine Lodge"
│   │   └── notes: "Bring warm clothes!"
│   │
│   ├── members/
│   │   ├── 0: { id: "abc123", name: "Alice", color: "#38bdf8" }
│   │   └── 1: { id: "def456", name: "Bob",   color: "#4ade80" }
│   │
│   ├── expenses/
│   │   └── 0: { id: "...", desc: "Ski passes", amount: 240,
│   │            payer: "abc123", splitBetween: ["abc123","def456"],
│   │            category: "ski", date: "2026-03-15T10:00:00" }
│   │
│   ├── activities/
│   │   └── 0: { id: "...", date: "2026-03-16", time: "09:00",
│   │            name: "Morning ski", desc: "Blue runs" }
│   │
│   ├── packingItems/
│   │   └── 0: { id: "...", name: "Ski goggles", category: "Ski Gear",
│   │            assignee: "abc123", checked: false }
│   │
│   └── currency: "EUR"
│
├── ski27_password                  <-- Hashed group password
└── ski27_admin_password            <-- Hashed admin password
```

> **Beginner tip:** Firebase Realtime Database stores data as a big JSON tree. Think of it like a nested JavaScript object that lives on Google's servers instead of in your browser.

### Real-Time Sync

The magic of Firebase is **real-time listening**. The app sets up a listener:

```javascript
dataRef.on('value', function(snapshot) {
    data = snapshot.val();
    renderAll();
});
```

This means: "Whenever *anything* changes in the database, give me the new data and re-render the whole screen." If Alice adds an expense on her phone, Bob's phone gets the update instantly.

---

## Authentication (Who Can Access What)

The app has a **two-level password system**:

| Role    | Can Do                                                      |
| ------- | ----------------------------------------------------------- |
| Admin   | Everything: manage passwords, reset data, import/export     |
| Member  | Use the app: add expenses, activities, packing items        |

### How Login Works (Step by Step)

```
1. User types a password
2. Browser hashes it with SHA-256 (turns it into a long string of characters)
3. App compares the hash to the stored hash in Firebase
4. If they match → user is logged in
5. Session is saved in sessionStorage (lasts until the tab is closed)
```

> **Beginner tip:** Hashing is a one-way process. You can turn a password into a hash, but you cannot turn a hash back into a password. This means even if someone sees the hash, they do not know the password.

### Firebase Anonymous Auth

The app also uses Firebase Anonymous Authentication. This is not for user identity — it simply gives the browser permission to talk to the Firebase database. It happens automatically in the background.

---

## Data Flow (How Everything Connects)

Here is what happens when a user adds an expense, as an end-to-end example:

```
1. User fills in the "Add Expense" form and taps "Save"

2. JavaScript reads the form values:
   { desc: "Lunch", amount: 45, payer: "abc123", ... }

3. A new expense object is pushed into `data.expenses`

4. `save()` is called, which writes `data` to Firebase:
   firebase.database().ref('ski27').set(data)

5. Firebase stores the data and notifies ALL connected clients

6. Every client's `on('value')` listener fires

7. Each client updates its local `data` and calls `renderAll()`

8. All screens now show the new expense
```

This is the core loop of the entire app: **User Action → Update State → Save to Firebase → Sync to All Devices → Re-render**.

---

## Key Concepts for Beginners

### What is a Single-Page Application (SPA)?

A traditional website loads a new HTML page every time you click a link. An SPA loads **one page** and then uses JavaScript to swap content in and out. Ski27 does this — when you tap the "Expenses" tab, it does not load a new page; it just hides the overview and shows the expenses section.

### What is Firebase?

Firebase is a set of cloud services by Google. This app uses two of them:

- **Realtime Database** — A cloud database that syncs data instantly across all connected devices. No need to build a backend server.
- **Authentication** — Manages who is allowed to access the database.

### What is the Web Crypto API?

A built-in browser feature for cryptographic operations. The app uses it to hash passwords with SHA-256. No external library is needed — it is built into every modern browser.

### What is a CDN?

CDN stands for Content Delivery Network. The Firebase SDK is loaded from a CDN:
```html
<script src="https://www.gstatic.com/firebasejs/10.14.1/firebase-app-compat.js"></script>
```
This means the JavaScript file is hosted on Google's servers and downloaded by the user's browser. You do not need to include the Firebase code in your project — the browser fetches it from the internet.

### Why No Framework (React, Vue, etc.)?

Frameworks like React are great for large apps with many developers. For a small, focused app like Ski27, plain JavaScript keeps things simple:

- **No build step** — You do not need to run `npm install` or `npm run build`.
- **No tooling** — No Webpack, Vite, or Babel to configure.
- **Easy to deploy** — Upload one file and you are done.
- **Easy to understand** — What you see is what runs.

The trade-off is that larger apps become harder to maintain without a framework, but for this scope it works well.

---

## Running the App Locally

Since the app is a single HTML file, you can run it by simply opening `index.html` in your browser. However, for Firebase to work properly, you should serve it over HTTP:

**Option 1 — Python (if installed):**
```bash
cd Ski27
python3 -m http.server 8000
# Open http://localhost:8000 in your browser
```

**Option 2 — Node.js (if installed):**
```bash
npx serve Ski27
# Open the URL shown in your terminal
```

**Option 3 — VS Code:**
Install the "Live Server" extension, right-click `index.html`, and select "Open with Live Server".

---

## Summary

| Question                        | Answer                                               |
| ------------------------------- | ---------------------------------------------------- |
| What language is it written in? | HTML, CSS, and vanilla JavaScript                    |
| Where is the data stored?       | Firebase Realtime Database (Google Cloud)             |
| How many files make up the app? | One — `index.html`                                   |
| Do I need to install anything?  | No. Just open the file in a browser                  |
| Does it need a backend server?  | No. Firebase handles the backend                     |
| How does real-time sync work?   | Firebase pushes updates to all connected clients      |
| How are passwords stored?       | As SHA-256 hashes in Firebase                        |
| Is it mobile-friendly?          | Yes. It is designed mobile-first                     |
