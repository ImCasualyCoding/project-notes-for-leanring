# MoveRx Website: Architecture & Organization 🏗️

The MoveRx codebase evolved from a collection of static pages (Version 1) to a modular, dynamic client-side architecture (Version 2). This note explains the directory layouts, structural decoupling, and cache-busting strategies we used.

---

## 📂 Codebase Directory Structures

Here is how the project files are organized in the workspace:

### Version 1: Static Architecture (`mollies-webpage`)
Version 1 was structured as flat, page-level documents where HTML, CSS, and styling rules were directly coupled.
```text
mollies-webpage/
├── index.html               # Main Landing Page (HTML with inline text content)
├── index.css                # Landing Page Styles
├── credibility.html         # Credentials & Bio Page (Static HTML)
├── credibility.css          # Credentials Page Styles
├── testimonials.html        # Testimonials list (Static HTML)
├── testimonials.css         # Testimonials Styles
├── scroll-leaves.js         # Leaf Animation Engine
├── images/                  # Client and structural image directories
└── certs/                   # Document scan directories
```

### Version 2: Decoupled Dynamic Architecture (`mollies-page-v2`)
In Version 2, we separated all page contents, textual copies, configurations, and SVGs into dedicated data files. The page structure was transformed into a modular layout:
```text
mollies-page-v2/
├── index.html               # Landing Page (serves as the main HTML container)
├── the-mission.html         # Credentials & Bio container
├── testimonials.html        # Testimonials container
└── assets/                  # Central Assets Directory
    ├── css/
    │   ├── variables.css    # Design System Token Values (colors, typography)
    │   ├── main.css         # Universal layouts (nav, footer, container structures)
    │   └── mission.css      # Bio & Specialties specific designs
    ├── js/
    │   ├── main.js          # Core nav drawer toggles and global scripts
    │   ├── mission-data.js  # DECOUPLED DATA: Texts, stats, specialties, leaf layouts
    │   ├── mission.js       # RENDER ENGINE: Builds mission/credentials sections dynamically
    │   ├── testimonials-data.js # DECOUPLED DATA: Testimonials & success entries
    │   └── testimonials.js  # RENDER ENGINE: Renders testimonials & 3D tilt cards
    ├── images/              # Client photo assets
    └── certs/               # Certification scans
```

---

## 💡 Decoupled Data-Driven Pattern

### The Problem in Version 1
In Version 1, if Mollie wanted to edit her qualifications or change a sentence in her bio, she had to navigate through complex HTML hierarchies, find the specific text node, and edit it. This risked breaking HTML structural tags and made updates error-prone.

### The Solution in Version 2
We implemented a **Client-Side Rendering (CSR)** structure using Vanilla Javascript. All editable text and items are stored in standard JavaScript objects inside `xxx-data.js` files, and a dedicated `xxx.js` engine reads this data to build the DOM elements.

*   **Read more about this concept**: **[[Concepts/Web Development/Dynamic DOM Injection|Dynamic DOM Injection]]**

#### Code Example: How Content is decopled
In `assets/js/mission-data.js`:
```javascript
const missionContent = {
    hero: {
        title: "The Mission & Credentials.",
        subtext: "Discover Mollie's evidence-based clinical specialties..."
    },
    principles: {
        heading: "Mollie’s Practice & Principles",
        paragraphs: [
            "Under the <strong>MoveRx</strong> framework, I merge <strong>corrective exercise</strong>..."
        ]
    }
};
```

In `assets/js/mission.js` (Rendering Engine):
```javascript
function renderHero() {
    const mainContent = document.getElementById("main-content");
    if (!mainContent) return;

    const section = document.createElement("section");
    section.className = "mission-hero animate-fade-in";

    const container = document.createElement("div");
    container.className = "container mission-hero-content";

    const h1 = document.createElement("h1");
    h1.textContent = missionContent.hero.title; // Injects Title from Data file
    container.appendChild(h1);

    const p = document.createElement("p");
    p.textContent = missionContent.hero.subtext; // Injects Subtext from Data file
    container.appendChild(p);

    section.appendChild(container);
    mainContent.appendChild(section);
}
```

---

## ⚡ Cache Busting System

### The Problem: Sticky CSS Styles
During deployment updates, browsers frequently cache CSS stylesheets and JS files locally. When we made updates to the layout or fixed responsiveness bugs, returning users often saw broken designs because their browsers loaded the old cached CSS file alongside the new HTML layout.

### The Solution: Query String Versioning
To force browsers to fetch the updated files from the server, we implemented version query parameters on all asset links.

*   **Read more about this concept**: **[[Concepts/Web Development/Cache Busting|Browser Cache Busting]]**

By appending `?v=2.1.0` to the file path, we declare a unique resource address. Whenever we modify a CSS file, we increment the version query parameter in the HTML:
```html
<!-- Busting browser cache by forcing a refresh of version 2.1.0 -->
<link rel="stylesheet" href="assets/css/variables.css?v=2.1.0">
<link rel="stylesheet" href="assets/css/main.css?v=2.1.0">
<link rel="stylesheet" href="assets/css/mission.css?v=2.1.0">

<!-- Injected JS Files with version tags -->
<script src="assets/js/mission-data.js?v=2.1.0"></script>
<script src="assets/js/mission.js?v=2.1.0"></script>
```
Whenever a stylesheet changes, changing the version string (e.g., to `?v=2.1.1`) instantly breaks the browser cache, guaranteeing that the client sees the exact correct styles immediately.
