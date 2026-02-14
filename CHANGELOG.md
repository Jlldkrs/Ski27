# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

---

## [1.0.0] - 2026-02-14

First stable release of Ski27.

### Features

- **Trip management** — Set destination, dates, accommodation, and notes.
- **Group members** — Add and remove members with auto-assigned colors.
- **Expense tracking** — Record expenses with description, amount, category, payer, and split. Edit and delete expenses.
- **Balance & settlements** — View individual balances and simplified settlement suggestions.
- **Schedule** — Add, edit, and delete activities with dates and times, grouped chronologically.
- **Packing list** — Categorized items assigned to members with check-off tracking and progress bars.
- **Personal profile** — View your expenses, balance, and packing progress.
- **Settings** — Currency selection (EUR, USD, GBP, CHF), password management, data export/import, and full reset.
- **Real-time sync** — All data syncs instantly across devices via Firebase Realtime Database.
- **Two-level access control** — Admin and member roles with separate passwords.
- **Toast notifications** — Success feedback after actions.
- **Mobile-first design** — Optimized for phones with safe area support and touch-friendly UI.

### Security

- Client-side SHA-256 password hashing via Web Crypto API.
- Firebase Anonymous Authentication for database access.
- Admin-only restrictions on sensitive actions (password changes, data reset, export/import).

### Documentation

- `ARCHITECTURE.md` — Full technical walkthrough of the app, beginner-friendly.
- `README.md` — Project overview, quick start, and feature summary.
- `CONTRIBUTING.md` — Guidelines for contributing to the project.
- `LICENSE` — MIT License.
- `CHANGELOG.md` — This file.
