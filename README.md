# AIObar

> A zero-dependency WordPress plugin that makes every blog post LLM-citation ready.

![WordPress](https://img.shields.io/badge/WordPress-5.8%2B-blue?logo=wordpress)
![PHP](https://img.shields.io/badge/PHP-7.4%2B-purple?logo=php)
![License](https://img.shields.io/badge/License-GPL--2.0%2B-green)
![Dependencies](https://img.shields.io/badge/Dependencies-zero-brightgreen)

---

## What It Does

AI tools like Claude, ChatGPT, and Perplexity are increasingly how people discover and consume content. When someone reads your post and wants to summarise, cite, or discuss it with an AI, the experience is usually clunky — copy the URL, open a new tab, paste, write a prompt.

**AIObar removes all of that friction.**

It adds a toolbar directly above every blog post with four tools:

| Feature | What it does |
|---|---|
| **Social Share** | One-click buttons for Facebook, LinkedIn, X (Twitter), Reddit |
| **Summarize** | Dropdown that opens Claude, ChatGPT, or Perplexity pre-filled with a summarise prompt for the current post |
| **Copy for LLM** | Copies `Title + URL + full plain-text body` to clipboard — structured exactly how AI chat expects it |
| **View Markdown** | Shows the post body as plain text in a collapsible dark panel — useful for inspecting what an AI will actually read |

The **Copy for LLM** and **Summarize** features are the core of the LLM-citation story: readers can get your content into any AI conversation in one click, with the title and URL intact — meaning the AI has everything it needs to attribute your post as a source.

Pure PHP + vanilla JavaScript. No npm, no Composer, no icon fonts, no external HTTP requests at runtime.

---

## Installation

### Option A — Upload via WordPress Admin (recommended)

1. Download or clone this repository.
2. Zip the folder containing `aiobar.php`.
3. In WordPress Admin go to **Plugins → Add New → Upload Plugin**.
4. Upload the zip, click **Install Now**, then **Activate Plugin**.
5. Visit any single post — the toolbar appears above the content.
6. **Purge all caches** (see [Caching](#caching) below).

### Option B — Manual FTP/SFTP

1. Upload `aiobar.php` to `/wp-content/plugins/aiobar/`.
2. Activate via **Plugins → Installed Plugins**.
3. Purge caches.

### Option C — WP-CLI

```bash
wp plugin install https://github.com/slayerman420/aiobar/archive/refs/heads/main.zip --activate
```

---

## Configuration

All visual design and behaviour options are PHP constants. The plugin ships with neutral defaults that work with any theme. Override any constant in `wp-config.php` **before** the `/* That's all, stop editing! */` line.

### Visual Constants

| Constant | Default | Controls |
|---|---|---|
| `AIOBAR_PRIMARY_COLOR` | `#000000` | Button fill on hover / Summarize active state |
| `AIOBAR_PRIMARY_HOVER` | `#222222` | Reserved hover shade |
| `AIOBAR_BORDER_COLOR` | `#E5E5E5` | Ghost button borders, toolbar divider |
| `AIOBAR_TEXT_COLOR` | `#555555` | Button label text |
| `AIOBAR_FONT_FAMILY` | `system-ui, sans-serif` | All toolbar text |
| `AIOBAR_BORDER_RADIUS` | `8px` | Buttons, dropdown, panel corners |

### Behaviour Constants

| Constant | Default | Controls |
|---|---|---|
| `PUBLIC_BASE_URL` | `''` (empty) | Rewrites share URLs for subdomain/proxy setups (see below) |

---

## Tweaking for Your Theme & Blog Guidelines

### 1 — Match Your Brand Colour

```php
// wp-config.php
define( 'AIOBAR_PRIMARY_COLOR', '#e63946' );
define( 'AIOBAR_PRIMARY_HOVER', '#c1121f' );
```

### 2 — Match Your Typography

```php
define( 'AIOBAR_FONT_FAMILY', '"Inter", system-ui, sans-serif' );
```

CSS custom properties work too:

```php
define( 'AIOBAR_FONT_FAMILY', 'var(--wp--preset--font-family--body, system-ui)' );
```

### 3 — Match Your Corner Radius

```php
define( 'AIOBAR_BORDER_RADIUS', '999px' );  // pill
define( 'AIOBAR_BORDER_RADIUS', '0px' );    // square
```

### 4 — Theme Example — Dark News Site

```php
define( 'AIOBAR_PRIMARY_COLOR', '#ff6b35' );
define( 'AIOBAR_PRIMARY_HOVER', '#e85d2a' );
define( 'AIOBAR_BORDER_COLOR',  '#2e2e2e' );
define( 'AIOBAR_TEXT_COLOR',    '#aaaaaa' );
define( 'AIOBAR_FONT_FAMILY',   '"DM Sans", sans-serif' );
define( 'AIOBAR_BORDER_RADIUS', '4px' );
```

### 5 — Theme Example — Minimal Lifestyle Blog

```php
define( 'AIOBAR_PRIMARY_COLOR', '#3d5a80' );
define( 'AIOBAR_PRIMARY_HOVER', '#2c4a6e' );
define( 'AIOBAR_BORDER_COLOR',  '#dde3ea' );
define( 'AIOBAR_TEXT_COLOR',    '#4a4a4a' );
define( 'AIOBAR_FONT_FAMILY',   '"Lora", Georgia, serif' );
define( 'AIOBAR_BORDER_RADIUS', '6px' );
```

### 6 — Theme Example — SaaS / Tech Company Blog

```php
define( 'AIOBAR_PRIMARY_COLOR', '#6366f1' );
define( 'AIOBAR_PRIMARY_HOVER', '#4f46e5' );
define( 'AIOBAR_BORDER_COLOR',  '#e0e7ff' );
define( 'AIOBAR_TEXT_COLOR',    '#374151' );
define( 'AIOBAR_FONT_FAMILY',   '"Inter", system-ui, sans-serif' );
define( 'AIOBAR_BORDER_RADIUS', '8px' );
```

### 7 — Override Spacing via the Customizer

Add rules in **Appearance → Customize → Additional CSS**:

```css
.aiobar-toolbar {
    margin-bottom: 16px !important;
    padding-top: 8px !important;
}

.aiobar-social-btn {
    width: 34px !important;
    height: 34px !important;
}
```

### 8 — Hiding Individual Buttons

```css
/* Additional CSS in Customizer */
[data-aiobar-action="share-reddit"]         { display: none !important; }
[data-aiobar-action="summarize-perplexity"] { display: none !important; }
[data-aiobar-action="copy-llm"]             { display: none !important; }
[data-aiobar-action="view-markdown"]        { display: none !important; }
```

### 9 — Dark-Mode Themes

```css
.aiobar-toolbar             { border-bottom-color: #333 !important; }
.aiobar-social-btn,
.aiobar-action-btn          { border-color: #444 !important; color: #ccc !important; }
.aiobar-summarize-dropdown  { background: #1e1e1e !important; border-color: #444 !important; }
.aiobar-summarize-dropdown button       { color: #ccc !important; }
.aiobar-summarize-dropdown button:hover { background: #2a2a2a !important; }
```

### 10 — Restricting to Specific Post Categories

```php
// In aiobar.php — replace both is_single() guards with this
function aiobar_should_show() {
    return is_single() && in_category( array( 'tech', 'tutorials' ) );
}
```

---

## PUBLIC_BASE_URL — Subdomain & Reverse-Proxy Setups

### The Problem

Many publishers run WordPress on an internal subdomain (e.g. `blog.mycompany.com`) but serve content publicly through a reverse proxy at a different URL (e.g. `mycompany.com/blog`). WordPress's `get_permalink()` returns the internal URL, so share links point to the wrong domain — and when an AI cites the source, it cites the wrong URL.

### The Solution

```php
// wp-config.php
define( 'PUBLIC_BASE_URL', 'https://mycompany.com/blog' );
```

| WordPress permalink | Resulting share URL |
|---|---|
| `https://blog.mycompany.com/how-we-built-our-api/` | `https://mycompany.com/blog/how-we-built-our-api/` |

Leave `PUBLIC_BASE_URL` undefined (or `''`) for standard single-domain installs.

---

## Caching

If the toolbar is invisible after activation, clear caches in this order:

1. **WordPress cache plugin** — WP Super Cache, W3 Total Cache, LiteSpeed Cache
2. **Hosting panel** — Kinsta, WP Engine, SiteGround
3. **CDN** — Cloudflare: *Caching → Purge Everything*
4. **Browser** — `Ctrl + Shift + R` (Win/Linux) or `Cmd + Shift + R` (Mac)

---

## Architecture

### Why no `<script>` tags inside `the_content`?

Browsers refuse to execute JavaScript injected via `innerHTML` — the HTML spec classifies such scripts as inert. This plugin uses `wp_enqueue_script()` + `wp_add_inline_script()` so the JS is emitted as a proper footer `<script>` tag.

### Why event delegation?

```js
document.addEventListener('click', function(e) {
  const btn = e.target.closest('[data-aiobar-action]');
  if (!btn) return;
});
```

If the toolbar HTML is injected after `DOMContentLoaded` fires (common with caching layers or page builders), element-level listeners would find nothing to bind to. Document-level delegation works regardless of when the element was added to the DOM.

### Why `is_single()` instead of URL path checks?

`strpos($_SERVER['REQUEST_URI'], ...)` breaks behind a reverse proxy. `is_single()` evaluates WordPress's internal query object and works in all deployment topologies.

### Why zero external dependencies?

No CDN fonts, no icon libraries, no tracking pixels. Zero extra network requests. GDPR-friendly out of the box.

---

## File Structure

```
aiobar/
└── aiobar.php   ← entire plugin in one file
```

---

## Browser Support

| Browser | Minimum |
|---|---|
| Chrome / Edge | 88+ |
| Firefox | 78+ |
| Safari | 14+ |
| Samsung Internet | 13+ |

IE not supported.

---

## Changelog

### 1.0.0 — 2025-07-02
- Initial release as AIObar
- Social share: Facebook, LinkedIn, X, Reddit
- Summarize dropdown: Claude, ChatGPT, Perplexity
- Copy for LLM with clipboard fallback
- View Markdown collapsible panel
- Configurable via PHP constants (`AIOBAR_*`)
- `PUBLIC_BASE_URL` for subdomain/proxy setups
- Zero external dependencies

---

## Contributing

Pull requests welcome. Please open an issue first to discuss the change.

---

## License

GPL-2.0+  
https://www.gnu.org/licenses/gpl-2.0.html
