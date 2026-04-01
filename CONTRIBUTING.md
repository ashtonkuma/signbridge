# Contributing to SignBridge

Thank you for your interest in contributing! SignBridge is a single-file, no-build-step project — contributions are straightforward.

## How to Contribute

### Reporting Bugs
Open an issue with:
- Which phase file (`phase1.html`, `phase2.html`, `phase3.html`)
- Browser and version
- Steps to reproduce
- Expected vs. actual behavior

### Suggesting Features
Open an issue labeled `enhancement` describing:
- The problem you want to solve
- Your proposed solution
- Which user (deaf/mute signer or hearing person) benefits

### Submitting Code

1. Fork the repository
2. Create a branch: `git checkout -b feature/your-feature-name`
3. Make your changes in the relevant phase HTML file
4. Test in Chrome (primary target) and Firefox
5. Commit with a clear message: `git commit -m 'Add: two-hand detection toggle'`
6. Push and open a Pull Request

## Code Style

- Each phase is a **single self-contained HTML file** — no external JS files, no bundler
- CSS variables for all colors (see `:root` block)
- JavaScript: vanilla ES6+, no frameworks
- Keep the file under ~150KB unminified

## Priority Areas

- **Gesture classifier accuracy** — additional letter patterns, reduced false positives
- **Sign dictionary** — more words, better animation configs
- **NLP rules** — more ASL→English grammar patterns
- **Accessibility** — ARIA labels, keyboard navigation, high-contrast mode
- **Mobile** — responsive layout improvements for portrait orientation
- **ISL / BSL** — classifier variants for other sign languages

## Questions?

Open a GitHub Discussion or an issue labeled `question`.
