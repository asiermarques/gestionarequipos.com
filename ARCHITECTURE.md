# Architecture — gestionarequipos.com

Hugo site for curating team management and leadership resources (books). Spanish-only, no theme — fully custom.

---

## Stack

| Concern | Tool |
|---|---|
| SSG | Hugo 0.161.0 extended (built with ≥ 0.134) |
| CSS | SCSS → Hugo native compiler (libsass) |
| CSS framework | Bootstrap 5 (git submodule at `vendor/bootstrap/`) |
| Fonts | Google Fonts CDN — Aleo + Mulish (whole site); Bebas Neue + Cormorant Garamond (hero "cover" block only) |
| Images | Hugo image processing → webp, q70, Lanczos |
| JS | None |
| Deployment | Netlify (`hugo --minify`) |
| Base URL | https://gestionarequipos.com/ |

Build command: `git submodule update --init --recursive && hugo --minify`

---

## Directory layout

```
assets/
  img/                  # Source images (logo, favicons, author, book covers)
    resources/          # Book cover images
  scss/
    main.scss           # Entry point — tokens, Bootstrap import + all partials
    header.scss
    libro.scss
    newsletter.scss
    autor.scss
    resources.scss
    footer.scss
content/
  recursos/
    _index.md           # Library landing (layout = 'resources')
    *.md                # One file per book/resource
layouts/
  _default/
    baseof.html         # Base template
    home.html           # Homepage
    resources.html      # /recursos/ list page
    resource.html       # Single resource detail
    book.html           # Single book detail (same pattern as resource)
    taxonomy.html       # Taxonomy fallback
  partials/
    head.html
    head/
      meta.html         # SEO meta, OG, Twitter, canonical, favicon, feeds
      schema.html       # JSON-LD structured data
      css.html          # SCSS compilation + Google Fonts
    header.html         # Nav bar
    footer.html         # Author bio + social links + slim site footer
    libro.html          # Homepage hero section
    newsletter.html     # Newsletter CTA card
    resources-summary.html  # Latest 4 books (used on homepage)
    resources/
      book-item.html    # Reusable book card component
static/
  file/
    valve.es.2012.pdf   # Downloadable PDF
vendor/
  bootstrap/            # Git submodule — Bootstrap 5 SCSS source
archetypes/
  default.md            # Hugo new content template
hugo.toml               # Site config
netlify.toml            # Netlify build config
```

---

## Hugo config (`hugo.toml`)

```toml
baseURL = 'https://gestionarequipos.com/'
languageCode = 'es'
title = "Gestionar Equipos"

[params]
description = 'Recursos e ideas sobre gestión de equipos en español'
```

---

## Content model

### Book (`type = "book"`)

Front matter (TOML):
```toml
+++
title = "Book Title"
type = "book"
layout = "book"
authors = ["Author Name"]
image = "img/resources/cover.webp"
+++
Content / description in Markdown...
```

Template: `layouts/_default/book.html`

### Resource (`type = "resource"`)

Same as book but can include a `link` field for external URLs:
```toml
+++
title = "Resource Title"
type = "resource"
layout = "resource"
link = "https://..."
image = "img/resources/cover.png"
+++
```

Template: `layouts/_default/resource.html`

### Section index

`content/recursos/_index.md`:
```toml
+++
title = 'Biblioteca de recursos'
layout = 'resources'
+++
```

---

## Template hierarchy

```
baseof.html
  └─ {{ block "main" . }}
       ├─ home.html          → partials: libro, resources-summary, newsletter
       ├─ resources.html     → ranges all books, uses book-item partial
       ├─ book.html          → single book detail
       └─ resource.html      → single resource detail

partials always loaded:
  head.html → head/meta.html + head/schema.html + head/css.html
  header.html
  footer.html (author bio + site footer)
```

`book.html` and `resource.html` share one layout shell (`#resource-detail`): a
`.resource-cover` column + a content column with `.resource-label`, an `<h1>`
title, `.authors-line`, and `.description`. `resource.html` adds a `.resource-link`
button when the front matter has a `link`. Keep these class names — the styling in
`resources.scss` depends on them, and the `<h1>` is the page's only top-level heading.

---

## SCSS / Design tokens

Defined in `assets/scss/main.scss`:

```scss
// Color
$black:            #1a1a1a;
$primary:          #aa4c26;   // terracotta (accent)
$secondary:        #1B2E4B;   // navy
$white:            #f7f6f7;
$beige:            #eadec3;
$beige-dark:       #d4c5a3;
$cream:            #faf8f4;    // main background
$navy-deep:        #0f1e32;
$terracotta-light: #c8694a;
$terracotta-pale:  #f3e8e2;

// Type
$font-size-base:       1rem;
$font-family-base:     "Mulish", sans-serif;
$headings-font-family: "Aleo", serif;
$font-display:         "Bebas Neue", sans-serif;        // hero title only
$font-serif-cover:     "Cormorant Garamond", serif;     // hero subtitle/byline only

// Shape
$enable-rounded:   false;
$radius:           0;
$space:            5rem;
```

Bootstrap variables are set **before** the Bootstrap import so they override defaults. See `DESIGN.md` for how these tokens are meant to be used (accent rules, contrast, background rhythm).

Production build: CSS is compressed and fingerprinted. Dev: source maps enabled.

---

## Image processing pattern

All images go through Hugo's image pipeline. Standard pattern:

```go-html
{{ with resources.Get .Params.image }}
  {{ with .Resize (printf "%dx webp q70 lanczos" .Width) }}
    <img src="{{ .Permalink }}" class="img-fluid" alt="">
  {{ end }}
{{ end }}
```

- Always converts to webp
- Quality 70%, Lanczos resampling
- Resizes to intrinsic width (preserves aspect ratio)
- Processed images cached in `resources/_gen/`

---

## Adding a new book

1. Create `content/recursos/slug.md` with the front matter above (`type = "book"`, `layout = "book"`)
2. Drop the cover image into `assets/img/resources/`
3. Reference it as `image = "img/resources/filename.ext"` in front matter
4. Body content is free Markdown — appears as the book description

To create via CLI: `hugo new recursos/slug.md` (uses `archetypes/default.md`).

---

## Adding a new content type

1. Add a new layout file in `layouts/_default/<type>.html`
2. Use `layout = "<type>"` in front matter
3. If it needs a list view, create `layouts/_default/<section>.html`
4. Add a new SCSS file under `assets/scss/` and import it in `main.scss`

---

## SEO / meta

`layouts/partials/head/meta.html` handles:
- `<title>` — dynamic per page
- `<meta name="description">` — uses `.Params.description` or site default
- OpenGraph (title, description, image, type)
- Twitter Card
- JSON-LD structured data
- Canonical URL
- RSS autodiscovery
- Favicon (`assets/img/favicon.png`, served as webp)

---

## Navigation

Hardcoded in `layouts/partials/header.html`. To add a nav item, edit that file directly. Active state is detected via `{{ if eq .RelPermalink "/" }}` style comparisons.

---

## Deployment

Netlify reads `netlify.toml`. No CI secrets needed. Pushes to `main` trigger a deploy automatically. The `public/` directory is git-ignored — it is built by Netlify each time.

Submodule must be initialized: the build command handles this with `git submodule update --init --recursive`.
