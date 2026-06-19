# CSS Design Systems: Variables & Root Token Management 🎨

A design system is a collection of reusable style tokens (colors, typography, spacing, shadows) that ensure visual consistency across all pages of a website. In modern CSS, we implement design systems using **CSS Custom Properties** (also known as **CSS Variables**).

This note documents variable syntax, global scopes (`:root`), local overrides, and token architectures.

---

## 1. Defining CSS Custom Properties

CSS variables allow you to store value configurations in one place and reuse them throughout your stylesheets.

### A. Declaration Syntax
Variable names must begin with a double hyphen (`--`):
```css
.card {
  --card-bg: #ffffff; /* Variable declaration inside local scope */
}
```

### B. Usage Syntax
To apply a variable value, wrap the variable name inside the `var()` function:
```css
.card {
  background-color: var(--card-bg);
}
```

### C. Fallback Values
You can declare a fallback value that the browser will use if the custom property is undefined or fails to load:
```css
.card {
  /* Use --card-bg, fallback to white if --card-bg is missing */
  background-color: var(--card-bg, #ffffff); 
}
```

---

## 2. Global vs. Local Scope

CSS variables follow the Cascade and inheritance rules of normal properties. Their availability depends on where they are declared:

### A. Global Scope (`:root`)
The `:root` pseudo-class represents the highest parent element in the HTML document (`<html>`). Declaring variables inside `:root` makes them globally accessible to all elements on the page.

```css
:root {
  --primary-color: #0d9488; /* Globally accessible */
}
```

### B. Local Scope & Local Overrides
You can redeclare a global variable inside a specific class selector to change its value only for that component and its children:

```css
:root {
  --text-color: #ffffff; /* Default: White text */
}

/* Local override for light blocks */
.light-section {
  --text-color: #0f172a; /* Changes text color to slate only inside this block */
}

p {
  color: var(--text-color); /* Automatically inherits the correct contextual color */
}
```

---

## 3. Designing a Modern Token System

Here is a copy-pasteable design system structure based on the MoveRx variables layout:

```css
/* Declared inside assets/css/variables.css */
:root {
  /* 1. Color Tokens */
  --bg-primary: #0a0f18;      /* Deep Slate Dark */
  --bg-secondary: #111a2e;    /* Card Slate */
  --text-primary: #f8fbfd;     /* Crisp Off-White */
  --text-secondary: #94a3b8;   /* Muted Gray */
  
  /* Accent Colors */
  --accent-teal: #14b8a6;      /* Brand Accent (Corrective Teal) */
  --accent-gold: #e5b05c;      /* Contrast Accent (Sand Gold) */

  /* 2. Typography Tokens */
  --font-heading: 'Playfair Display', Georgia, serif;
  --font-body: 'Outfit', sans-serif;
  
  /* Font Sizes */
  --size-xs: 12px;
  --size-sm: 14px;
  --size-md: 16px;
  --size-lg: 20px;
  --size-xl: 32px;
  --size-xxl: 48px;

  /* 3. Layout / Spacing Tokens */
  --radius-sm: 6px;
  --radius-md: 12px;
  --radius-lg: 24px;
  
  --transition-smooth: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

/* Consuming the design system tokens */
body {
  background-color: var(--bg-primary);
  color: var(--text-primary);
  font-family: var(--font-body);
  font-size: var(--size-md);
}

h1 {
  font-family: var(--font-heading);
  font-size: var(--size-xxl);
  color: var(--accent-gold);
}

.success-card {
  background-color: var(--bg-secondary);
  border-radius: var(--radius-md);
  transition: var(--transition-smooth);
}
```

---

## 🚀 Why Design Systems Use CSS Variables
1.  **Single Source of Truth**: If Mollie decides to change her brand accent from Teal to Blue, you only need to edit a single line inside the `:root` block (`--accent-teal`). The change propagates instantly to all buttons, backgrounds, and badges across the entire website.
2.  **No Compilation Required**: Unlike preprocessors (like Sass or Less) where variables are compiled away, CSS variables are natively parsed by the browser. You can inspect and edit them live in the browser's developer tools.
3.  **Dynamic JavaScript Access**: JavaScript can read and write CSS variables dynamically. This makes them ideal for implementing features like dynamic theme switching (Dark/Light mode) or scroll-based value updates.
    *   **Learn how JavaScript manipulates these tokens**: **[[Concepts/Languages/JavaScript/DOM API|JavaScript DOM API]]**
    *   **Learn how these tokens are animated**: **[[Concepts/Languages/CSS/Animations & Transforms|CSS Animations & Transforms]]**
