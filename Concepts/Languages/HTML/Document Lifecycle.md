# HTML: Document Lifecycle & Script Execution ⏳

Understanding how the browser reads, parses, and executes an HTML document is critical for preventing loading blocks, ensuring fast performance, and preventing JavaScript crashes.

This note documents the lifecycle of a web page, the difference between document sections, and script-loading strategies.

---

## 1. Document Preamble & Structure

Every HTML document follows a strict envelope structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- Metadata & non-visual configs -->
</head>
<body>
    <!-- Visual structure & content -->
</body>
</html>
```

### A. The `<!DOCTYPE html>` Declaration
This is not an HTML tag; it is a preamble instruction telling the browser that this is an **HTML5** document. 
*   **Why it's required**: Without it, browsers fall back into **Quirks Mode**, rendering layouts using rules from 1990s browsers (e.g. Netscape/Internet Explorer), which breaks modern CSS styles. The Doctype forces **Standards Mode**.

### B. `<head>` vs `<body>` Responsibilities
*   **`<head>`**: Contains metadata that configured the document. It does not display on the screen. It holds the character encoding (`<meta charset="UTF-8">`), viewport rules, stylesheet link paths, external fonts, and scripts.
*   **`<body>`**: Contains all elements that are visible to the user (headings, text, buttons, images, cards).

---

## 2. Browser Parsing & Script Blocking

Browsers parse HTML documents sequentially, from line 1 down to the end of the file. As the browser parses tags, it builds the DOM tree node-by-node.

```text
  [Parser starts] -> [Reads <head>] -> [Downloads index.css] -> [Reads <body>] -> [Parser ends]
```

### The Blocking Script Problem
If the browser encounters a standard `<script src="app.js"></script>` tag, it **halts HTML parsing immediately**. 
1.  The browser stops building the DOM tree.
2.  It sends a network request to download `app.js`.
3.  It executes `app.js` once downloaded.
4.  Only after execution finishes does the browser resume parsing the remaining HTML.

```text
  [HTML Parsing...] ──> [Encounters <script>] ──> [HALT Parsing / Download JS / Run JS] ──> [Resume HTML Parsing...]
```

#### Why this causes bugs:
If your script tries to select a button (e.g. `document.getElementById("btn")`) that is declared on a line *below* the script tag, the selection returns `null` because the browser has not parsed that button element node yet!

---

## 3. Modern Script Loading: `async` vs `defer`

To prevent scripts from blocking the HTML parser, modern web design utilizes script attributes:

```html
<!-- 1. Blocking (Standard) -->
<script src="app.js"></script>

<!-- 2. Asynchronous -->
<script async src="app.js"></script>

<!-- 3. Deferred (Recommended for most scripts) -->
<script defer src="app.js"></script>
```

### Comparative Loading Timeline:

```text
HTML Parser  │ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █ █
─────────────┼──────────────────────────────────────────────
Standard JS  │ █ █ █ █ █ █ [Download & Run JS (HALT)] █ █ █
─────────────┼──────────────────────────────────────────────
async JS     │ █ █ █ █ █ [Download JS (BG)] [Run JS (HALT)] █
─────────────┼──────────────────────────────────────────────
defer JS     │ █ █ █ [Download JS (BG)] █ █ █ █ █ [Run JS]
```
*(BG = Background download)*

*   **`async`**: The script downloads in the background. The moment it finishes downloading, the HTML parser halts to run the script. Order of execution is not guaranteed (whichever downloads first runs first). Use only for independent scripts (e.g. third-party Google Analytics).
*   **`defer`**: The script downloads in the background. It **never halts** the parser. Execution is held until the HTML parser has completely finished building the DOM tree. Deferred scripts execute in the exact order they are declared in the HTML document.

---

## 4. Page Lifecycle Events

JavaScript can listen for specific checkpoints in the loading process:

### A. `DOMContentLoaded`
This event fires on the `document` object once the HTML parser has built the full DOM tree. External assets (images, frames, stylesheets) might still be downloading.
*   **Use case**: Ideal for initializing your page UI logic and binding click listeners to buttons.
```javascript
document.addEventListener("DOMContentLoaded", () => {
    console.log("DOM tree is ready! Safe to select elements.");
});
```

### B. `load`
This event fires on the global `window` object once the DOM tree is built **AND** all external resources (large photos, CSS stylesheets, frames) have completed loading.
*   **Use case**: Best for removing loading splash screens (preloaders) or starting canvas animations that require images.
```javascript
window.addEventListener("load", () => {
    console.log("Everything is fully loaded including stylesheets and images.");
});
```

---

## 5. Semantic HTML5 Elements

In older web pages, developers structured layouts using generic containers (`<div id="header">`, `<div class="footer">`). Modern HTML uses **semantic tags** that tell both the browser and search engine bots the exact purpose of the content.

### Core Semantic Tags:
*   `<header>`: Introduces a section or contains navigation links.
*   `<nav>`: Holds primary navigation links.
*   `<main>`: Wraps the core, unique content of the page (only one per page).
*   `<section>`: Groups related content together (e.g., a services list, a portfolio grid).
*   `<article>`: Wraps self-contained content that could be distributed independently (e.g., blog posts).
*   `<footer>`: Sits at the bottom of the page or section, holding copyright metadata, maps, or contact pills.

*   **Learn how semantic tags are styled**: **[[Concepts/Languages/CSS/Core Mechanics|CSS Core Mechanics]]**
*   **Learn how dynamic scripts mount onto these semantic layouts**: **[[Concepts/Languages/JavaScript/DOM API|JavaScript DOM API]]**
