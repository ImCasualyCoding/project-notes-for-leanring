# Browser Cache Busting & Asset Versioning ⚡

To optimize load times and save bandwidth, web browsers store copies of static assets (like CSS, JavaScript, and images) locally on the user's computer. When the user returns to a website, the browser loads these files from disk cache instead of requesting them from the hosting server.

However, if you update styles or script files on your server, returning users might see a broken page because their browser loads a **new HTML structure** alongside **old cached CSS styles**. Cache busting resolves this mismatch.

---

## 1. How Caching Works (The HTTP Level)

When a web server delivers a file, it specifies caching instructions in the HTTP response headers:
*   `Cache-Control: max-age=31536000`: Tells the browser it can cache this file for up to one year.
*   `ETag`: A unique token representing the specific file version. The browser checks this token against the server to see if the file has changed before loading it.

If headers are set aggressively, browsers bypass server checks altogether, loading cached resources instantly.

---

## 2. Strategies for Cache Busting

We can force the browser to invalidate its cache and fetch fresh copies using three methods:

### Method A: Query Parameter Versioning (Manual)
Append a version string as a query parameter (`?v=1.0.0`) to the asset link. Since the query parameter makes the file path unique, the browser treats it as a brand-new resource and fetches it from the server.

```html
<!-- Version 2.0.0 -->
<link rel="stylesheet" href="assets/css/main.css?v=2.0.0">

<!-- When styles change, increment the query parameter -->
<link rel="stylesheet" href="assets/css/main.css?v=2.1.0">
```

*   **Pros**: Simple, fast, and does not require complex build tools. Excellent for smaller vanilla JS websites (such as the MoveRx project).
*   **Cons**: Some older CDN proxy servers fail to cache files containing query parameters, causing unnecessary load on your origin server.

---

### Method B: Content Hash Filenames (Automated)
Modern bundlers (like Vite, Webpack, or Rollup) compute a cryptographic hash (like MD5) of the file contents and inject that hash directly into the filename during compilation (e.g. `main.a4f10c8e.css`).
*   If the file contents change by even a single character, the hash changes completely.
*   The bundler then automatically updates the links inside `index.html` to point to the new filename.

```text
  [Source: main.css] (Modified)
         │
         ▼ (compile & hash)
  [Build: main.8f92bd3a.css] (New filename generated)
         │
         ▼ (automounts reference)
  [index.html] -> <link href="main.8f92bd3a.css">
```

*   **Pros**: Fully automated, reliable, and perfectly cached by all CDNs.
*   **Cons**: Requires a build pipeline (Node.js/npm) and compiles files into a `dist/` directory.

---

### Method C: HTTP Cache Headers
You can configure your web server (Nginx, Apache, Netlify, or Vercel) to tell the browser not to cache specific files, or to check for updates on every request.

For HTML files (which point to all other resources), you should always disable caching:
```http
Cache-Control: no-cache, no-store, must-revalidate
```
For CSS/JS with hashed names, you can safely cache forever since the name will change if the content does:
```http
Cache-Control: public, max-age=31536000, immutable
```

---

## 🛠️ Implementation in MoveRx

In the MoveRx website (`mollies-page-v2`), we opted for **Method A (Query Parameter Versioning)**. Since the codebase is written in vanilla JS without build tools, appending query variables directly inside HTML files allowed us to control updates easily:

```html
<!-- Decoupled JS engines with version queries -->
<script src="assets/js/mission-data.js?v=2.1.0"></script>
<script src="assets/js/mission.js?v=2.1.0"></script>
```
Whenever we modified the code inside `mission.js`, we incremented the query parameter to `?v=2.1.1` in `the-mission.html` to force returning clients to download the latest visual rendering logic.
