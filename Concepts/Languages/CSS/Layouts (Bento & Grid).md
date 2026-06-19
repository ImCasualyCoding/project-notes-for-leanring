# CSS Layouts: Flexbox, Grid, & Responsive Bento Boxes 🍱

To organize elements on a web page, CSS provides two primary layout engines: **Flexbox** (for one-dimensional alignments) and **Grid** (for two-dimensional layouts).

This note documents these layout systems, the creation of modern Bento Grid layouts, and mobile-responsive viewport selectors.

---

## 1. CSS Flexbox (One-Dimensional Layouts)

Flexbox arranges elements in a single direction (either horizontally as a row or vertically as a column). It is designed to distribute space and align items inside container elements.

### The Axis Model:
*   **Main Axis**: The primary direction of layout flow. Controlled by `flex-direction`.
    *   `flex-direction: row` (Default): Main axis runs left to right.
    *   `flex-direction: column`: Main axis runs top to bottom.
*   **Cross Axis**: The direction perpendicular to the main axis.

```text
               Main Axis (row flow) ───>
         ┌──────────────────────────────────────┐
       │ ┌─────────┐   ┌─────────┐   ┌─────────┐ │
  C    │ │  Item1  │   │  Item2  │   │  Item3  │ │
  r    │ └─────────┘   └─────────┘   └─────────┘ │
  o    └────────────────────────────────────────┘
  s
  s Axis (downward)
  ▼
```

### Core Flexbox Properties:
*   `display: flex`: Activates the flex layout context on the parent container.
*   `justify-content`: Aligns items along the **Main Axis** (options: `flex-start`, `center`, `flex-end`, `space-between`, `space-around`, `space-evenly`).
*   `align-items`: Aligns items along the **Cross Axis** (options: `flex-start`, `center`, `flex-end`, `stretch`).
*   `flex-wrap: wrap`: Allows child elements to wrap onto new lines if they run out of container space.
*   `flex-grow: 1`: Forces an item to expand and consume any leftover empty space.

---

## 2. CSS Grid (Two-Dimensional Layouts)

CSS Grid arranges elements in both columns and rows simultaneously. It is ideal for complete page designs and asymmetrical card grids.

### Core Grid Properties:
*   `display: grid`: Activates the grid context.
*   `grid-template-columns`: Declares the number and size of columns.
*   `grid-template-rows`: Declares the sizes of rows.
*   `gap` (or `column-gap` / `row-gap`): Defines the gutters between grid boxes.

### The Fractional Unit (`fr`)
The `fr` unit represents a fraction of the free space available in the grid container.
```css
.grid-container {
  display: grid;
  grid-template-columns: 1fr 2fr 1fr; /* 3 columns: Center column is twice as wide */
  gap: 20px;
}
```

---

## 3. Creating a Bento Grid Layout

A **Bento Grid** is an asymmetrical, dashboard-style grid layout named after traditional Japanese bento lunchboxes. It features multiple box cards of different heights and widths that align to a uniform grid structure.

We achieve this in CSS Grid using column and row span properties:
*   `grid-column: span N`: Forces an item to stretch across `N` grid columns.
*   `grid-row: span N`: Forces an item to stretch across `N` grid rows.

### Bento Grid Code Example:
```html
<div class="bento-container">
    <div class="bento-card card-large">Stats</div>
    <div class="bento-card card-tall">Bio Text</div>
    <div class="bento-card card-wide">Certifications</div>
    <div class="bento-card">Mini Info</div>
</div>
```

```css
.bento-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3 equal-width columns */
  grid-auto-rows: 200px; /* Base height for row rows */
  gap: 16px;
}

/* Card Spanning configurations */
.card-large {
  grid-column: span 2; /* spans 2 columns */
  grid-row: span 2;    /* spans 2 rows (400px + gap) */
  background-color: #0f172a;
}

.card-tall {
  grid-column: span 1; /* spans 1 column */
  grid-row: span 2;    /* spans 2 rows */
  background-color: #1e293b;
}

.card-wide {
  grid-column: span 2; /* spans 2 columns */
  grid-row: span 1;    /* spans 1 row */
  background-color: #334155;
}
```

---

## 4. Responsive Design & Media Queries

Websites must adapt dynamically to screens of varying sizes (mobile devices, tablets, ultra-wide monitors). We use **Media Queries** to apply specific CSS rules only when the browser window matches defined screen size limits.

### A. Mobile-First Architecture
Mobile-first means writing standard styling rules optimized for mobile screens first (without media queries), then using media queries to expand the layout for tablets and desktops as the screen width increases.

### B. Grid Resizing Example:
To prevent our 3-column Bento Grid from getting squashed on a mobile viewport, we collapse the layout into a single column on smaller screens:

```css
/* 1. Mobile Default: Single column layout */
.bento-container {
  display: grid;
  grid-template-columns: 1fr; /* single column */
  gap: 16px;
}

/* 2. Tablet & Desktop: Expand columns at viewport >= 768px wide */
@media (min-width: 768px) {
  .bento-container {
    grid-template-columns: repeat(3, 1fr); /* Expand to 3 columns */
  }
  
  .card-large {
    grid-column: span 2;
    grid-row: span 2;
  }

  .card-tall {
    grid-row: span 2;
  }

  .card-wide {
    grid-column: span 2;
  }
}
```

*   **Learn how to create animations inside layouts**: **[[Concepts/Languages/CSS/Animations & Transforms|CSS Animations & Transforms]]**
*   **Learn how layout variables are configured globally**: **[[Concepts/Languages/CSS/Design Systems|CSS Design Systems]]**
