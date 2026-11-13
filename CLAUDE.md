# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A GitBook-synced Markdown repository for a Czech-language Python course (Python ecosystem, language basics, data structures, functions, OOP, practical topics, then scientific/ML libraries). There is no code, build, lint, or test tooling — only Markdown. Content is written in Czech; keep new text in Czech.

Commits named `GITBOOK-N: ...` come from the GitBook web editor syncing to this repo, so expect upstream changes to `SUMMARY.md` and pages; pull before editing.

## Structure

- `SUMMARY.md` — GitBook table of contents; defines navigation and page order. Every new page must be linked here (sections are `##` headings, separated by `***`).
- `README.md` — course landing page ("Úvod do kurzu") containing the full 12-chapter outline (Python Ekosystém → PyTorch Lightning).
- Chapter pages in the repo root (`python-ekosystem.md`, `predstaveni-jazyka.md`, `zakladni-datove-struktury.md`, `funkce-a-jejich-volani.md`, `oop-v-pythonu.md`, `prakticke-pristupy.md`) — currently near-empty stubs awaiting content. `knihovny/page-1.md` is a placeholder for the library chapters (NumPy, Pandas, Matplotlib, SciPy, OpenCV, PyTorch Lightning).
- `.instructions/` — authoring notes, not published:
  - `content.md` — copy of the course outline.
  - `content-notes.md` — planning for the library chapters: each library's topics split into **Základní** (8–12 core concepts for a ~3-hour chapter) and **Rozšiřující** (extension material). Follow this split when writing those chapters.

## Writing study-material chapters

`python-ekosystem.md` is the reference chapter; new chapters should match its style. The user's brief was: take the bullet points for the chapter from `.instructions/content.md`, write study-support text covering them, ask when something is unclear.

- **Language and tone:** Czech, explanatory study-text addressed to students. Use full sentences and flowing paragraphs; minimize bullet lists (the reference chapter has none apart from code/trees).
- **Coverage:** cover every bullet of the chapter in `content.md`, in that order, each as its own `##` section (sub-topics as `###`), linked so that later sections build on earlier ones (e.g. refer forward/back: "o kterých bude řeč dále", "jak uvidíme dále"). Extra useful sections are fine (e.g. installation, Python versioning), placed where they fit logically.
- **Structure:** `# Title`, a short intro paragraph saying what the chapter covers and why, `##` sections, and a closing `## Shrnutí` paragraph.
- **Examples:** include fenced code blocks where they help — Python code (```python) and command-line commands (```console / ```powershell / ```toml / ```text for trees). Explain each example in prose before or after it. Prefer Windows-friendly commands (PowerShell) and add Linux/macOS variants where they differ.
- **Terms:** bold the key term on first introduction (e.g. **virtuální prostředí**) and keep the English term in parentheses or italics when it is the common one (*bytecode*, *end of life*).
- **Diagrams:** draw schemes and diagrams with Mermaid (```mermaid fenced blocks), not ASCII art. (Plain directory trees stay as ```text blocks.)
- **GitBook formatting:** the content is published through GitBook, so make full use of GitBook-flavoured Markdown where it improves the text — hints/callouts (`{% hint style="info|success|warning|danger" %}`), tabs (`{% tabs %}` / `{% tab title="..." %}`, e.g. for Windows vs. Linux/macOS commands), expandable sections, code blocks with titles, and similar GitBook blocks. Use standard Markdown for everything else.
- **Images:** wherever an image or screenshot would help (e.g. an IDE window, an installer dialog, a result plot, an explanatory illustration), insert a visible placeholder on its own paragraph instead of leaving the spot out, so the images can be added later. The placeholder is a `TODO` that contains a complete, short **prompt for generating the image**, in this form: `TODO: obrázek – Prompt: "<English prompt: what is shown, style (e.g. flat vector, screenshot), layout, labels (in Czech)>"`. Put the placeholder right after the paragraph or code block it illustrates. Do not use a plain description without a prompt.
- **Accuracy:** avoid hard-coding fast-changing facts (latest version numbers, EOL dates); link to the official source instead.
- **Library chapters** (NumPy, Pandas, Matplotlib, SciPy, OpenCV, PyTorch Lightning): follow the Základní / Rozšiřující split from `.instructions/content-notes.md` — core topics get full treatment, extension topics a shorter overview.

## Conventions

- Keep the README outline, `SUMMARY.md`, and `.instructions/content.md` consistent when chapters are added, renamed, or reordered.
- Page filenames are lowercase, ASCII, hyphenated slugs of the Czech title (diacritics stripped).
