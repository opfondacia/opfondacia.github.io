# opfondacia.github.io

Website of the Dimitar P. Kudoglu Foundation (Plovdiv, Central District). Jekyll + Bootstrap 5, published with GitHub Pages (Settings → Pages → Deploy from a branch → `main` / root). Bulgarian (default) and English.

## Run locally

```bash
bundle install
bundle exec jekyll serve
```

Then open http://localhost:4000 (Bulgarian) or http://localhost:4000/en/ (English).

## Languages (i18n)

GitHub Pages only builds with a fixed set of plugins, and none of them is an i18n plugin, so the translation is done with plain Jekyll:

| | Bulgarian (default) | English |
| --- | --- | --- |
| Home | `/` | `/en/` |
| History | `/history/` | `/en/history/` |
| Partners | `/partners/` | `/en/partners/` |
| Events | `/events/` | `/en/events/` |
| An event | `/events/<name>/` | `/en/events/<name>/` |
| Contacts | `/contacts/` | `/en/contacts/` |

- **All interface text** (menu, buttons, headings, quote, footer, month names) lives in `_data/i18n/bg.yml` and `_data/i18n/en.yml`. Edit the text there. The two files have the same keys.
- **Pages** are thin wrappers (`index.html`, `history/index.html`, `events/index.html`, `partners/index.html`, `contacts/index.html` and the same under `en/`) around shared bodies in `_includes/pages/`. Change the layout once, both languages follow. Each page has `lang` and `ref` in its front matter; pages with the same `ref` are translations of each other.
- **The language switch** in the header links to the translation of the current page (matched by `ref`). If a page has no translation it links to the other language's home page. `hreflang` tags are added for search engines.
- **Events** are one file per language: `_events/` (Bulgarian) and `_events_en/` (English). Give the two files of the same event the same `ref:` so the switch finds its translation.
- `contact.email`, `contact.phone`, `contact.map` and the social links are language-neutral and live in `_config.yml`. The contact address text is per language (`contacts.address` in the i18n files).

### Adding another language

1. Copy `_data/i18n/en.yml` to `_data/i18n/<code>.yml` and translate it.
2. Copy the `en/` folder to `<code>/`, and change `lang:`, `permalink:` and `title:` in the three pages.
3. Add an `events_<code>` collection and a matching `defaults` entry in `_config.yml`, plus an `_events_<code>/` folder.
4. The header switch currently toggles between two languages (`_includes/lang-alt.html`); it needs a small change to offer three or more.

## Structure

| Path | Purpose |
| --- | --- |
| `_config.yml` | Site settings, contact details, collections |
| `_data/i18n/` | Translated texts (`bg.yml`, `en.yml`) |
| `_data/navigation.yml` | Menu items (labels come from the i18n files) |
| `_layouts/`, `_includes/` | Templates (`event.html`, `event-tournament.html`), header, footer, event card, partner logo grid, map, language helpers |
| `_includes/pages/` | Bodies of the home, history, events, partners, media and contacts pages |
| `_events/`, `_events_en/` | One file per event and language |
| `_data/partners.yml` | Partners (name, logo, link): shown on the Partners page, and events can list some of them as sponsors |
| `assets/` | CSS and images |
| `_brand-source/` | Full-size originals (not published) |

## History page

Everything on `/history/` comes from the `history_page` block in `_data/i18n/bg.yml` and `en.yml` (the two must have the same entries, in the same order). Each entry of `moments` has `year`, `title`, `photo`, `caption` (the subtitle under the photo) and `text`. Photos go in `assets/images/history/`. Below 1200px each photo is on its own row with the text under it; from 1200px (xl) the photo alternates between the left and right side. The texts and photos in the files are placeholders (marked `TODO`) and must be replaced.

## "In the media" page

`/media/` shows a card for each entry of `media_page.items` in `_data/i18n/bg.yml` and `en.yml`: `source` (the outlet, shown as a pill on the photo), `title`, `subtitle`, `image` (a path under `assets/images/media/`) and `url` (opens in a new tab). An entry with an empty `url` is hidden. The entries in the files are placeholders (marked `TODO`).

## Partners page

`/partners/` shows every entry of `_data/partners.yml` as a logo tile (linked to the partner's site when `url` is set). To add a partner, drop its logo (SVG or PNG) into `assets/images/sponsors/` and add an entry (`id`, `name`, `logo`, `url`) to `_data/partners.yml`. The intro text and the closing call-to-action are `partners_page` in the i18n files.

## Adding an event

1. Put the pictures in `assets/images/events/`.
2. Create `_events/YYYY-MM-DD-short-name.md` (Bulgarian) and `_events_en/YYYY-MM-DD-short-name.md` (English) with the same file name and the same `ref`:

```markdown
---
ref: short-name                              # the same in both languages
title: Заглавие на събитието
subtitle: Кратко подзаглавие
date: 2026-10-15
time: "10:00 – 15:00"                        # optional
location: Пловдив, район Централен           # optional, shown in details and on the card
map: Пловдив, район Централен                # optional: address or "lat,lng". No map when omitted.
image: /assets/images/events/my-event.jpg   # cover picture
image_alt: Описание на снимката
summary: Едно-две изречения за картата (по желание).
register_url: https://example.org/signup     # optional, default is an email to contact.email
cta_label: Присъединете се                   # optional button text
gallery:                                     # optional extra pictures
  - src: /assets/images/events/my-event-2.jpg
    alt: Описание
---
Описанието на събитието, написано с Markdown.
```

3. Commit and push to `main`. The event card appears on `/events/` and links to `/events/short-name/` (the date prefix is dropped from the URL).

Inline pictures inside the description: `![alt]({{ '/assets/images/events/photo.jpg' | relative_url }})`.

### Sponsors and the tournament template

The charity chess tournament has its own layout, `_layouts/event-tournament.html` (large date card, details and map, and a sponsors grid). An event uses it with `layout: event-tournament` in its front matter and lists its sponsors by id:

```markdown
layout: event-tournament
sponsors: [printable-heads, plovdiv-municipality, old-plovdiv]
```

- Sponsors are the partners defined once in `_data/partners.yml` (`id`, `name`, `logo`, `url`); the logo files are in `assets/images/sponsors/`. Any event can reuse them.
- To add a sponsor: drop its logo (SVG or PNG) into `assets/images/sponsors/`, add an entry to `_data/partners.yml`, and add its `id` to the event's `sponsors` list (in both languages). To remove one, delete it from the event's list.
- The logos are trademarks of their owners and were taken from the partners' own websites. Only list organizations that have agreed to be shown as partners or sponsors.

### Upcoming vs. past

Events dated today or later are listed under "Upcoming" (and drive the "Next event" strip on the home page); older ones go under "Past events". This is decided when the site is built, so an event moves to "Past" on the first push after its date.

## Maps

The map is an embedded Google Map, no API key needed. It is driven by an input parameter:

- Events: the `map` front-matter field. Leave it out and no map is shown.
- Contacts page: `contact.map` in `_config.yml`. Leave it empty and the "Find us" card is hidden.

Every map also gets a directions link.

## Look and feel

Styles are mobile-first (`assets/css/style.css`). The theme is beige ("dirty white") with the red `#701012` from the doors artwork. Colors, fonts and radius are CSS variables at the top of that file. Set `hero.image` in `_config.yml` for a photo behind the home page hero, and fill in `social` for the footer icons.

Headings use Montserrat, which has Bulgarian-specific Cyrillic letterforms that are switched on by `<html lang="bg">` (e.g. a cursive-style `т`). To use the standard forms everywhere, add `font-feature-settings: "locl" 0;` to the `h1…h6` rule.

### Doors intro

On every load of the home page, two ornamental doors with the logo cover the hero, swing open and fade away. The markup is at the top of `_includes/pages/home.html`, the animation is the "Hero intro" block in `style.css` (timings are in that block), and the artwork is:

- `assets/images/kolona-left.webp`, `kolona-right.webp`: the two door leaves (cropped, tinted to the theme and compressed from the originals).
- `assets/images/kudoglu-logo.png`: the logo shown on the seam.

The full-size originals (`kolona-left.png`, `kolona-right.png`, the logo) are kept in `_brand-source/`, which is not published. Visitors with "reduce motion" enabled, or without JavaScript, skip the intro.
