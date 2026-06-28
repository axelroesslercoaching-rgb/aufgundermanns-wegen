# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Static HTML/CSS mockup for **Auf Gundermanns Wegen** — a duo website for Axel Stiller & Uwe Kotteck. No build step, no framework, no bundler. Five hand-coded HTML files served directly.

## Dev server

```bash
npx serve -l 3456 .
```

The preview tool uses port 8899 (configured in `.claude/launch.json`). For browser access use port 3456.

## File overview

| File | Page |
|---|---|
| `index.html` | Startseite (alle Sections: Hero, Programm, Konzerte, Über uns, Medien, Kontakt) |
| `termine.html` | Konzerttermine |
| `ueber-uns.html` | Über das Duo |
| `media.html` | Medien / Videos |
| `kontakt.html` | Kontakt / Buchung |

All CSS lives inline in each file's `<style>` block. There is no shared stylesheet.

## Design system (inline in each file)

CSS custom properties defined in `:root`:

- `--schwarz` `#141A14`, `--dunkel` `#1C261C`, `--mittel` `#263626` — background shades
- `--gold` `#E8E0D0`, `--gold-dim` — primary text/accent
- `--weiss` `#F0EDE6`, `--weiss-dim` — body text
- `--gruen` `#2D5E26`, `--gruen-hell` `#4B8A42` — green accents
- `--rot` — red CTA (Termine button)
- Fonts: `'Caslon Antique'` (serif display, local woff2/woff in `assets/`) + `'Source Sans 3'` (Google Fonts, body)

## Hero architecture (index.html)

Three-column CSS grid of `<figure class="hero-photo">` elements with `position: absolute; inset: 0` fills the viewport. A `::before` gradient overlay sits at `z-index: 1`. The `.hero-content` text is at `z-index: 2`. Photo credits (`<div class="hero-credits">`) are a separate absolute layer at `z-index: 3` — **not inside `.hero-photos`** — because `.hero-photos` creates its own stacking context (z-index 0) that would hide credits behind the overlay.

## Assets

- `assets/hero-axel.png`, `assets/hero-gundi.png`, `assets/hero-uwe.png` — equal thirds (640×1080) split from a single Canva export. Do not trim borders; the outer spacing is intentional.
- `assets/CaslonAntique*.woff2/.woff` — local font files (must be inside project root so the dev server can serve them).
- Font paths in `@font-face` must be relative to the HTML file, e.g. `url('assets/CaslonAntique.woff2')`, never `../`.

## Konzerttermine pflegen (WordPress)

Alle Termine stehen in `konzerte.json`. Workflow bei neuem Termin oder Pflege:

1. `konzerte.json` aktualisieren — neuen Termin eintragen (ISO-Datum `YYYY-MM-DD`)
2. Automatisch ermitteln: alle Einträge mit `datum < heute` → vergangene Konzerte
3. WordPress-Seite (Post-ID 7) neu generieren und hochladen:
   ```bash
   # SSH: wp post update 7 --post_content="$(cat termine_content.txt)"
   ```
4. Commit + Push

**Der User gibt nur den neuen Termin (Stadt, Venue, Datum, Uhrzeit, Tags) — alles andere läuft automatisch.**

Felder in `konzerte.json`:
- `datum` — ISO-Format `YYYY-MM-DD` (für Sortierung und Vergleich mit heute)
- `stadt`, `venue`, `adresse`, `uhrzeit`
- `tags` — Array, z.B. `["Ostseetour"]` oder `[]`

## Legal

The Gerhard Gundermann photo (`hero-gundi.png`) is licensed **CC BY-SA 3.0** (Claude Lebus / Wikimedia Commons). The attribution must remain visible on the rendered page at all times.
