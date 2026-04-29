# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running locally

```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

Must be served over HTTP — `fetch('./content.json')` won't work over `file://`. If port 8080 is taken: `lsof -ti :8080 | xargs kill -9`

## Deploying

```bash
git add <files> && git commit -m "..." && git push   # triggers nothing automatically
vercel --prod --yes                                   # deploy to https://fashikom.vercel.app
```

## Architecture

Two files only — no build step, no npm, no framework.

| File | Role |
|---|---|
| `index.html` | All HTML structure, CSS (embedded `<style>`), and JS (embedded `<script>`) |
| `content.json` | All visible text in Hebrew (`"he"`) and English (`"en"`), including full article HTML |

### How content rendering works

On load, `init()` fetches `content.json`, detects language (`localStorage('fashicom-lang')` → browser locale → `'he'`), then calls `render()`. Every visible string comes from the JSON — nothing is hardcoded in HTML except element IDs.

The three DOM helpers used everywhere:
- `set(id, txt)` — sets `textContent`
- `html(id, markup)` — sets `innerHTML`
- `attr(id, key, val)` — sets an attribute

`renderCards()` and `renderArticles()` regenerate entire sections as HTML strings and inject via `innerHTML`. They also set `--delay` CSS custom properties for staggered reveal animations.

### content.json structure

```
{
  "he": { meta, lang_toggle, nav, hero, methods, about, articles_section, contact, footer, wa_float_aria },
  "en": { ...same keys... },
  "articles": [
    { "id", "icon", "color",
      "he": { title, summary, date, content },   ← content is raw HTML
      "en": { title, summary, date, content }
    }
  ]
}
```

The `articles` array is top-level (not inside `"he"`/`"en"`) because structure is shared; only the text fields are language-keyed.

### Language switching

`toggleLang()` flips `lang`, saves to `localStorage`, re-runs `render()` + `setupObserver()`. Switching also updates `document.documentElement.dir` (`rtl`/`ltr`) and `lang` attribute, so CSS logical properties like `inset-inline-start` adapt automatically.

### Scroll animations

`setupObserver()` attaches an `IntersectionObserver` to all `.reveal` elements. When an element enters the viewport it gets `.visible` (which transitions opacity + translateY). Called after every `render()` call because `renderCards` / `renderArticles` create new `.reveal` nodes.

Stagger delay is a CSS custom property set inline: `style="--delay:110ms"`.

### Contact details to replace

- Email: find `SHIRLY_EMAIL_PLACEHOLDER` in `index.html` (×2) — replace with real address
- Photo: `./src/shirili_main_photo.png` — already in place

### Color palette

All CSS colors use custom properties `--p50` through `--p900` (purple scale). The hero and contact sections use dark purple gradients; methods and about sections use `--bg` / `--p50`.
