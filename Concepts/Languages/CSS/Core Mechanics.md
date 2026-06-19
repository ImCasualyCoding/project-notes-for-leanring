# CSS: Core Mechanics (Cascade, Specificity, & Box Model) 🎨

Cascading Style Sheets (CSS) describe how HTML element nodes are rendered visually on the screen. For a novice, CSS can seem unpredictable because styles overwrite each other through complex algorithms.

This note documents the underlying rules of style resolution: **The Cascade**, **Specificity calculations**, and the **Box Model**.

---

## 1. Structure of a CSS Ruleset

A ruleset is a block of styling code containing a selector and declarations.

```text
  Selector ───> .success-card {
                  background-color: #ffffff; ──┐
                  border-radius: 12px;       ──┼── Declarations
                  padding: 24px;             ──┘
                     ▲            ▲
                     │            └─ Value
                     └─ Property
                }
```

*   **Selector**: Defines which HTML elements will be styled.
*   **Property**: The styling trait to change (e.g. `margin`, `color`, `display`).
*   **Value**: The setting applied to the property (e.g., `24px`, `#ffffff`, `flex`).

---

## 2. The Cascade Engine

The word **Cascading** means that rules trickle down from multiple stylesheets, and conflicts are resolved in a specific order:

1.  **Origin**: Browser defaults (User Agent) < User settings < Author styles (your code).
2.  **Specificity**: If multiple selectors target the same element, the selector with the higher specificity score wins.
3.  **Order of Appearance**: If origin and specificity are identical, the rule declared **last** in the stylesheet wins.

---

## 3. Specificity Mathematics 🧮

When multiple CSS selectors target the same element, the browser calculates a specificity score using a four-digit registry:

$$\text{Specificity} = (\text{Inline Styles},\, \text{IDs},\, \text{Classes},\, \text{Elements})$$

| Selector Category | Specificity Score | Examples |
| :--- | :--- | :--- |
| **Inline Styles** | `(1, 0, 0, 0)` | `<div style="color: red;">` |
| **IDs** | `(0, 1, 0, 0)` | `#contact`, `#hero-section` |
| **Classes / Attributes / Pseudo-classes** | `(0, 0, 1, 0)` | `.success-card`, `[tabindex="0"]`, `:hover` |
| **Elements / Pseudo-elements** | `(0, 0, 0, 1)` | `div`, `h1`, `p`, `::before` |
| **Universal Selector** | `(0, 0, 0, 0)` | `*` |

### Specificity Scoring Examples:
*   `div p`: **`(0, 0, 0, 2)`** (Two elements)
*   `.success-card p`: **`(0, 0, 1, 1)`** (One class, one element)
*   `.site-footer .contact-pill span`: **`(0, 0, 2, 1)`** (Two classes, one element)
*   `#contact .contact-pill`: **`(0, 1, 1, 0)`** (One ID, one class) — *Overwrites all selectors above because the ID column is higher.*

> [!CAUTION]
> The `!important` rule overrides all specificity logic. Avoid using it because it breaks the Cascade, making it extremely difficult to debug style conflicts later. Use selector adjustments to increase specificity naturally.

---

## 4. CSS Property Inheritance

Inheritance determines whether a child element automatically receives styling properties from its parent.

*   **Inherited Properties (Typography & Color)**: Properties like `font-family`, `color`, `line-height`, and `text-align` automatically inherit down parent-child chains. Defining `font-family: 'Outfit';` on `<body>` instantly updates all text on the page.
*   **Non-Inherited Properties (Layout & Structure)**: Properties like `margin`, `padding`, `border`, `width`, `height`, and `display` do not inherit. Setting a border on `<body>` does not put borders on all your paragraphs.

---

## 5. The CSS Box Model

Every element in HTML is treated as a rectangular box. The box has four structural layers that sit around the content:

```text
  ┌────────────────────────────────────────────────────────┐
  │                        MARGIN                          │
  │   ┌────────────────────────────────────────────────┐   │
  │   │                    BORDER                      │   │
  │   │   ┌────────────────────────────────────────┐   │   │
  │   │   │                PADDING                 │   │   │
  │   │   │   ┌────────────────────────────────┐   │   │   │
  │   │   │   │            CONTENT             │   │   │   │
  │   │   │   │         (Text / Image)         │   │   │   │
  │   │   │   └────────────────────────────────┘   │   │   │
  │   │   └────────────────────────────────────────┘   │   │
  │   └────────────────────────────────────────────────┘   │
  └────────────────────────────────────────────────────────┘
```

1.  **Content**: The inner core where text and images live.
2.  **Padding**: Transparent space *inside* the border, separating content from borders.
3.  **Border**: The boundary line surrounding the padding and content.
4.  **Margin**: Transparent space *outside* the border, separating the element from its neighbors.

---

## ⚡ The Critical Setting: `box-sizing`

By default, the browser calculates the total layout width of an element using the following formula:
$$\text{Total Width} = \text{width} + \text{left padding} + \text{right padding} + \text{left border} + \text{right border}$$

This causes layout bugs: if you set `width: 100%` on an input and add `padding: 10px`, the element grows to `100% + 20px` wide, breaking out of its container boundaries.

### The Solution: Global `border-box`
To solve this, modern layouts apply a global resets in CSS to set the box model calculations to `border-box`. This forces padding and borders to shrink inward, preserving the width value.

```css
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
```

With `box-sizing: border-box`, setting `width: 300px` guarantees the box is exactly `300px` wide. Any padding or borders you add will be squeezed inside that 300px limit.

*   **Learn how boxes are aligned on a layout**: **[[Concepts/Languages/CSS/Layouts (Bento & Grid)|CSS Layouts (Grid & Flexbox)]]**
*   **Learn how boxes are manipulated programmatically**: **[[Concepts/Languages/JavaScript/DOM API|JavaScript DOM API]]**
