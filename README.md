# Blog Content Toolbar

> A zero-dependency WordPress plugin that adds a social sharing and AI utility toolbar to every single blog post.

![WordPress](https://img.shields.io/badge/WordPress-5.8%2B-blue?logo=wordpress)
![PHP](https://img.shields.io/badge/PHP-7.4%2B-purple?logo=php)
![License](https://img.shields.io/badge/License-GPL--2.0%2B-green)
![Dependencies](https://img.shields.io/badge/Dependencies-zero-brightgreen)

---

## What It Does

The toolbar renders directly above the post body on every `is_single()` page and gives readers four tools in one row:

| Feature | What it does |
|---|---|
| **Social Share** | One-click buttons for Facebook, LinkedIn, X (Twitter), Reddit — each opens a pre-filled share dialog in a new tab |
| **Summarize** | Dropdown offering Claude, ChatGPT, and Perplexity — each pre-filled with a "please summarise this article" prompt |
| **Copy for LLM** | Copies `Title + URL + full plain-text body` to clipboard in one click — ideal for pasting into any AI chat |
| **View Markdown** | Toggles a collapsible dark-background panel showing the post body as plain text |

Pure PHP + vanilla JavaScript. No npm, no Composer, no icon fonts, no external HTTP requests at runtime.

---

## Installation

### Option A — Upload via WordPress Admin (recommended)

1. Download or clone this repository.
2. Zip the `blog-content-toolbar/` folder.
3. In WordPress Admin go to **Plugins → Add New → Upload Plugin**.
4. Upload the zip, click **Install Now**, then **Activate Plugin**.
5. Visit any single post — the toolbar will appear above the content.
6. **Purge all caches** (see [Caching](#caching) below).

### Option B — Manual FTP/SFTP

1. Upload the `blog-content-toolbar/` folder to `/wp-content/plugins/`.
2. Activate via **Plugins → Installed Plugins**.
3. Purge caches.

### Option C — WP-CLI

```bash
wp plugin install https://github.com/slayerman420/blog-content-toolbar/archive/refs/heads/main.zip --activate
```

---

## Configuration

All visual design and behaviour options are PHP constants defined at the top of the plugin. The plugin ships with neutral defaults that work with any theme. Override any constant in `wp-config.php` **before** the `/* That's all, stop editing! */` line.

### Visual Constants

| Constant | Default | Controls |
|---|---|---|
| `BCTB_PRIMARY_COLOR` | `#000000` | Filled button background on hover / Summarize active state |
| `BCTB_PRIMARY_HOVER` | `#222222` | Reserved hover shade |
| `BCTB_BORDER_COLOR` | `#E5E5E5` | Ghost button borders, toolbar divider |
| `BCTB_TEXT_COLOR` | `#555555` | Button label text and icon colour |
| `BCTB_FONT_FAMILY` | `system-ui, sans-serif` | All toolbar text |
| `BCTB_BORDER_RADIUS` | `8px` | Buttons, dropdown, markdown panel corners |

### Behaviour Constants

| Constant | Default | Controls |
|---|---|---|
| `PUBLIC_BASE_URL` | `''` (empty) | Rewrites share URLs for subdomain/proxy setups (see below) |

---

## Tweaking for Your Theme & Blog Guidelines

This section explains every lever available without touching the plugin file itself.

### 1 — Match Your Brand Colour

Grab your theme's primary hex value from its stylesheet and set `BCTB_PRIMARY_COLOR`:

```php
// wp-config.php
define( 'BCTB_PRIMARY_COLOR', '#e63946' );  // your brand red
define( 'BCTB_PRIMARY_HOVER', '#c1121f' );  // darker shade
```

### 2 — Match Your Typography

Mirror your theme's typeface:

```php
define( 'BCTB_FONT_FAMILY', '"Inter", system-ui, sans-serif' );
```

CSS custom properties work too, if your theme uses them:

```php
define( 'BCTB_FONT_FAMILY', 'var(--wp--preset--font-family--body, system-ui)' );
```

### 3 — Match Your Corner Radius

Pill-shaped buttons:
```php
define( 'BCTB_BORDER_RADIUS', '999px' );
```

Sharp square corners:
```php
define( 'BCTB_BORDER_RADIUS', '0px' );
```

### 4 — Full Theme Example — Dark News Site

```php
// wp-config.php
define( 'BCTB_PRIMARY_COLOR', '#ff6b35' );          // orange accent
define( 'BCTB_PRIMARY_HOVER', '#e85d2a' );
define( 'BCTB_BORDER_COLOR',  '#2e2e2e' );          // dark border
define( 'BCTB_TEXT_COLOR',    '#aaaaaa' );          // muted label text
define( 'BCTB_FONT_FAMILY',   '"DM Sans", sans-serif' );
define( 'BCTB_BORDER_RADIUS', '4px' );
```

### 5 — Full Theme Example — Minimal Lifestyle Blog

```php
// wp-config.php
define( 'BCTB_PRIMARY_COLOR', '#3d5a80' );          // muted navy
define( 'BCTB_PRIMARY_HOVER', '#2c4a6e' );
define( 'BCTB_BORDER_COLOR',  '#dde3ea' );
define( 'BCTB_TEXT_COLOR',    '#4a4a4a' );
define( 'BCTB_FONT_FAMILY',   '"Lora", Georgia, serif' );
define( 'BCTB_BORDER_RADIUS', '6px' );
```

### 6 — Full Theme Example — SaaS / Tech Company Blog

```php
// wp-config.php
define( 'BCTB_PRIMARY_COLOR', '#6366f1' );          // indigo
define( 'BCTB_PRIMARY_HOVER', '#4f46e5' );
define( 'BCTB_BORDER_COLOR',  '#e0e7ff' );
define( 'BCTB_TEXT_COLOR',    '#374151' );
define( 'BCTB_FONT_FAMILY',   '"Inter", system-ui, sans-serif' );
define( 'BCTB_BORDER_RADIUS', '8px' );
```

### 7 — Override Spacing via the Customizer

Add rules in **Appearance → Customize → Additional CSS** without touching the plugin:

```css
/* Push toolbar closer to the post title */
.bctb-toolbar {
    margin-bottom: 16px !important;
    padding-top: 8px !important;
}

/* Slightly smaller social icon tap targets */
.bctb-social-btn {
    width: 34px !important;
    height: 34px !important;
}
```

### 8 — Hiding Individual Buttons

```css
/* Additional CSS in Customizer */

/* Hide Reddit */
[data-bctb-action="share-reddit"]      { display: none !important; }

/* Hide Perplexity from Summarize dropdown */
[data-bctb-action="summarize-perplexity"] { display: none !important; }

/* Hide Copy for LLM */
[data-bctb-action="copy-llm"]          { display: none !important; }

/* Hide View Markdown */
[data-bctb-action="view-markdown"]     { display: none !important; }
```

### 9 — Dark-Mode Themes

```css
/* Additional CSS in Customizer */
.bctb-toolbar {
    border-bottom-color: #333 !important;
}
.bctb-social-btn,
.bctb-action-btn {
    border-color: #444 !important;
    color: #ccc !important;
}
.bctb-summarize-dropdown {
    background: #1e1e1e !important;
    border-color: #444 !important;
}
.bctb-summarize-dropdown button {
    color: #ccc !important;
}
.bctb-summarize-dropdown button:hover {
    background: #2a2a2a !important;
}
```

### 10 — Restricting to Specific Post Categories

By default the toolbar appears on all `is_single()` posts. To limit it, add a helper function to the plugin:

```php
function bctb_should_show() {
    return is_single() && in_category( array( 'tech', 'tutorials' ) );
}
```

Then replace both `if ( ! is_single() )` guards with `if ( ! bctb_should_show() )`.

---

## PUBLIC_BASE_URL — Subdomain & Reverse-Proxy Setups

### The Problem

Many publishers run WordPress on an internal subdomain (e.g. `blog.mycompany.com`) but serve content publicly through a reverse proxy at a different URL (e.g. `mycompany.com/blog`). WordPress's `get_permalink()` returns the internal URL, so share links point to the wrong domain.

### The Solution

```php
// wp-config.php
define( 'PUBLIC_BASE_URL', 'https://mycompany.com/blog' );
```

The plugin strips the scheme and host from the WordPress permalink and prepends your public base URL:

| WordPress permalink | Resulting share URL |
|---|---|
| `https://blog.mycompany.com/how-we-built-our-api/` | `https://mycompany.com/blog/how-we-built-our-api/` |

Leave `PUBLIC_BASE_URL` undefined (or set to `''`) for standard single-domain WordPress installs.

---

## Caching

If the toolbar is invisible after activation, the cause is almost always stale cache. Clear in this order:

1. **WordPress cache plugin**
   - WP Super Cache: *Settings → Delete Cache*
   - W3 Total Cache: *Performance → Purge All Caches*
   - LiteSpeed Cache: *LiteSpeed Cache → Purge All*

2. **Hosting panel cache**
   - Kinsta: MyKinsta → Clear Cache
   - WP Engine: *WP Engine → Purge All Caches*
   - SiteGround: *Speed → Caching → Flush Cache*

3. **CDN cache**
   - Cloudflare: *Caching → Configuration → Purge Everything*

4. **Browser hard reload**
   - Windows/Linux: `Ctrl + Shift + R`
   - Mac: `Cmd + Shift + R`

---

## Architecture

### Why no `<script>` tags inside `the_content`?

Browsers refuse to execute JavaScript injected via `innerHTML` as a fundamental security rule (the HTML spec classifies such scripts as "inert"). WordPress page builders, caching layers, and headless setups routinely write post content via `innerHTML`. Any `<script>` tag inside would silently do nothing.

This plugin uses `wp_enqueue_script()` + `wp_add_inline_script()` so the JS is emitted as a proper footer `<script>` tag that the browser fully trusts and executes.

### Why event delegation?

```js
document.addEventListener('click', function(e) {
  const btn = e.target.closest('[data-bctb-action]');
  if (!btn) return;
  // handle action
});
```

If the toolbar HTML is injected after `DOMContentLoaded` fires (common with caching layers or deferred content), element-level listeners bound during page load would find nothing to attach to. Document-level delegation intercepts clicks regardless of when the target element was added to the DOM.

### Why `is_single()` instead of URL path checks?

`strpos($_SERVER['REQUEST_URI'], ...)` breaks when WordPress runs behind a reverse proxy — the `REQUEST_URI` seen by PHP won't match the public URL. `is_single()` evaluates WordPress's internal query object and works correctly in all deployment topologies.

### Why zero external dependencies?

No CDN fonts, no icon libraries, no tracking pixels, no third-party scripts. The toolbar adds zero network requests and zero third-party cookies. This keeps the plugin GDPR-friendly and fast on every page load.

---

## File Structure

```
blog-content-toolbar/
├── blog-content-toolbar.php   ← Single-file plugin (620 lines)
└── README.md
```

Everything — PHP, CSS, JS, SVG icons — lives in one file. No build step. No Composer. No npm.

---

## Browser Support

| Browser | Minimum version |
|---|---|
| Chrome / Edge (Chromium) | 88+ |
| Firefox | 78+ |
| Safari | 14+ |
| Samsung Internet | 13+ |

Internet Explorer is not supported.

---

## Changelog

### 1.0.0 — 2025-07-02
- Initial release
- Social share: Facebook, LinkedIn, X, Reddit
- Summarize dropdown: Claude, ChatGPT, Perplexity
- Copy for LLM with clipboard fallback
- View Markdown collapsible panel
- Fully configurable via PHP constants
- `PUBLIC_BASE_URL` support for subdomain/proxy setups
- Zero external dependencies

---

## Contributing

Pull requests welcome. Please open an issue first to discuss what you'd like to change.

---

## License

GPL-2.0+
https://www.gnu.org/licenses/gpl-2.0.html
