# Contributing to Ski27

Thanks for your interest in contributing! This guide will help you get started.

## Getting Started

1. **Fork** the repository on GitHub.
2. **Clone** your fork locally:
   ```bash
   git clone https://github.com/<your-username>/Ski27.git
   cd Ski27
   ```
3. **Create a branch** for your changes:
   ```bash
   git checkout -b your-feature-name
   ```
4. **Make your changes** to `index.html`.
5. **Test** by opening the file in your browser and verifying everything works.
6. **Commit** and **push** your branch:
   ```bash
   git add index.html
   git commit -m "Add a short description of your change"
   git push origin your-feature-name
   ```
7. Open a **Pull Request** on GitHub.

## Project Layout

The entire app lives in a single file: `index.html`. It is organized into three sections in order:

1. **CSS** — Inside a `<style>` tag in the `<head>`.
2. **HTML** — The markup in the `<body>`.
3. **JavaScript** — Inside `<script>` tags at the end of the `<body>`.

## Code Style

- **No frameworks or build tools.** Keep it vanilla HTML, CSS, and JavaScript.
- **Indent with 4 spaces** (no tabs).
- **Use `const` and `let`**, never `var`.
- **Use descriptive function and variable names.** Clarity over brevity.
- **Keep functions small.** If a function is getting long, break it into smaller pieces.
- **CSS variables** — Use the existing CSS custom properties in `:root` for colors. Do not hardcode color values.

## What to Work On

- Check the **Issues** tab on GitHub for open tasks.
- Bug fixes and accessibility improvements are always welcome.
- For larger features, open an issue first to discuss the approach before writing code.

## Testing Your Changes

Since there is no automated test suite, please manually verify:

- [ ] The app loads without console errors.
- [ ] Your feature works on both mobile and desktop screen sizes.
- [ ] Existing features still work (expenses, schedule, packing list, login/logout).
- [ ] Data syncs correctly if you have Firebase configured.

## Commit Messages

Write clear, concise commit messages that explain **what** and **why**:

```
Good:  "Add expense category filter to expenses tab"
Good:  "Fix balance calculation when a member has no expenses"
Bad:   "Update index.html"
Bad:   "Fix stuff"
```

## Reporting Bugs

When reporting a bug, please include:

1. What you expected to happen.
2. What actually happened.
3. Steps to reproduce.
4. Browser and device info.

## Code of Conduct

Be respectful and constructive. We're all here to build something useful together.
