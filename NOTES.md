# Working notes

How this site is put together and how to change it.

## The core idea

There are two separate things, and they update with two separate commands.

```
your .qmd files  ──[ git push ]──────────>  main branch      (source)
       │
       │ quarto render
       ▼
    _site/        ──[ quarto publish ]────>  gh-pages branch  (the live site)
```

`git push` does not update the website.
It backs up the source.
`quarto publish gh-pages` is what changes what visitors see.
Normally you do both, and forgetting the second is the most common reason a site appears not to have updated.

## Files to edit

| File | Controls |
|---|---|
| `_quarto.yml` | Site-wide config: navbar, title, theme, search, footer. Most powerful file, and the easiest to break — YAML is indentation-sensitive. |
| `index.qmd` | Homepage: bio, photo, CV/GitHub/LinkedIn links. |
| `teaching.qmd` | Teaching page. Plain markdown, hand-maintained. |
| `research/index.qmd` | The Research *listing* page — intro text and listing settings. Not the papers. |
| `research/*.qmd` | One file per paper. A new file appears on the Research page automatically. |
| `styles.css` | Visual tweaks layered over the theme. The `.justified` rule lives here. |
| `images/` | Photos. |
| `files/` | CV. The PDF is published; the Word source is tracked in git but excluded from the site. |
| `README.md` | Shown on the GitHub repo page. Not part of the website. |
| `.gitignore` | What git ignores. |

## Files never to edit

| Thing | What it is |
|---|---|
| `_site/` | The built website. Regenerated on every render; edits here are destroyed. |
| `.quarto/` | Quarto's cache. Safe to delete when something looks stale. |
| `.git/` | Git internals. |
| `.Rproj.user/`, `.Rhistory` | RStudio session state. |
| `personal-site.Rproj` | RStudio project file. Double-click to open the project; otherwise leave alone. |

## Common tasks

### Add a paper

Copy an existing file in `research/` and edit the front matter.

```yaml
---
title: "Your Paper Title"
description: "Working paper — the one-line contribution"
date: "2027-03-01"
categories: [Catholic]
---
```

`date` controls sort order on the listing page and nothing else.
`description` is the line under the title on the card.
Keep the pattern the three existing entries use: **type — what it contributes**, not the venue.
Venue detail belongs in the entry body under a `**Presented.**` heading.

### Change the navbar

The `navbar:` block in `_quarto.yml`.

### Change the look

`theme: cosmo` in `_quarto.yml` swaps the whole palette; there are 25 built-in options.
Use `styles.css` for finer adjustments.

### Update the CV

Put the new PDF in `files/`, then update the filename in **two places**:

- `_quarto.yml` — the navbar CV entry
- `index.qmd` — the links block

Missing one leaves a dead link.
Naming the file plainly `resume.pdf` would remove this trap permanently.

### Change the photo

Put it in `images/` and update the `image:` line in `index.qmd`.
One place only.

## Gotchas

**Clear the cache after renaming or deleting a page.**
Quarto's listing index can hold a stale entry and render it as a blank card.
Deleting `_site/` alone is not enough — remove `.quarto/` and `_freeze/` too, then render.

**Do not run `quarto render` while `quarto preview` is running.**
Two Quarto processes writing `_site/` at once will kill the preview.
Stop the preview first.

**Prose is wrapped one sentence per line.**
`editor: markdown: wrap: sentence` in `_quarto.yml` makes RStudio's visual editor preserve this.
Removing that setting will let the visual editor rewrap everything to a fixed column.

**`freeze: auto` currently does nothing.**
It only matters on pages that run code, and there are no code chunks in the site right now.
It will start working the moment one is added.

## Working loop

Start the preview and leave it running.
Edits refresh the browser automatically.

```bash
quarto preview
```

`Ctrl+C` in that terminal stops it.
In a terminal, `Ctrl+C` means interrupt, not copy.

When the changes are ready:

```bash
git add -A
git commit -m "describe the change"
git push
quarto publish gh-pages
```

The first three save the source.
The last one updates the live site.

## If the local copy is ever lost

Everything that cannot be regenerated lives on GitHub.

```bash
git clone https://github.com/rrodriguezbarron/rrodriguezbarron.github.io.git
```

Then `quarto render` rebuilds `_site/` locally.
The published site keeps serving from the `gh-pages` branch regardless of what happens to the local folder.
