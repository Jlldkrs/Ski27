# Ski27

A collaborative holiday planner for group trips. Track expenses, plan activities, manage packing lists, and settle up — all synced in real time across everyone's devices.

## Features

- **Expense splitting** — Record who paid, split costs between members, and see settlement suggestions for who owes whom.
- **Schedule planning** — Add activities with dates and times, grouped chronologically.
- **Packing list** — Categorized items assigned to members with check-off progress tracking.
- **Trip overview** — Destination, dates, accommodation, and group member management.
- **Personal profile** — View your balance, expenses, and packing progress at a glance.
- **Real-time sync** — Powered by Firebase. Changes appear instantly on all connected devices.
- **Two-level access** — Admin and member roles with separate passwords.
- **Data backup** — Export and import trip data as JSON.

## Quick Start

Ski27 is a single HTML file with no dependencies to install.

### Option 1 — Open directly

Open `index.html` in your browser. That's it.

### Option 2 — Local server (recommended for full Firebase functionality)

```bash
# Python
python3 -m http.server 8000

# or Node.js
npx serve .
```

Then open `http://localhost:8000` in your browser.

### First-time setup

1. Open the app.
2. You will be prompted to create an **admin password**.
3. Optionally set a **group password** in Settings so others can join.
4. Share the group password with your trip members.

## Tech Stack

| Layer     | Technology                   |
| --------- | ---------------------------- |
| Frontend  | HTML, CSS, vanilla JavaScript |
| Database  | Firebase Realtime Database    |
| Auth      | Firebase Anonymous Auth       |
| Hashing   | Web Crypto API (SHA-256)      |

No frameworks, no build tools, no package manager. See [ARCHITECTURE.md](ARCHITECTURE.md) for a detailed technical walkthrough.

## Project Structure

```
Ski27/
├── index.html         # The entire application
├── ARCHITECTURE.md    # Technical deep-dive (beginner-friendly)
├── CONTRIBUTING.md    # How to contribute
├── CHANGELOG.md       # Version history
└── LICENSE            # MIT License
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to contribute.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
