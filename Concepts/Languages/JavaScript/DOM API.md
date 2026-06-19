# JavaScript: The DOM API (DOM Selection, Creation, & Mutation) 💉

The Document Object Model (DOM) is an object-oriented programming interface representing HTML documents in memory. The **DOM API** is the set of functions JavaScript uses to select, create, modify, or delete visual nodes on the screen.

This note documents element queries, dynamic element creation, class manipulations, and node mounting.

---

## 1. Selecting DOM Elements

To manipulate an element, you must first locate it. The modern DOM selection interface provides:

```javascript
// A. Query by ID (Very fast, returns a single element)
const footer = document.getElementById("nav-header");

// B. Query first match (Returns the first element matching a CSS selector)
const activeLink = document.querySelector(".nav-links .active-link");

// C. Query all matches (Returns a static NodeList containing all matches)
const successCards = document.querySelectorAll(".success-card");
```

### Working with a NodeList:
A `NodeList` is similar to an array but does not support all array methods (like `map` or `filter`). However, it does support `forEach` and indexing:
```javascript
successCards.forEach((card, index) => {
    console.log(`Card ${index}:`, card);
});
```

---

## 2. Programmatically Creating Elements

To inject dynamic content (like the specialties list in the MoveRx project), we build nodes in memory before mounting them.

```javascript
// 1. Create a container element
const newCard = document.createElement("div");

// 2. Add classes for styles
newCard.className = "success-card bento-style";

// 3. Create a child heading element
const heading = document.createElement("h3");
heading.textContent = "New Specialty"; // Safe text injection

// 4. Nest the heading inside the card
newCard.appendChild(heading);
```

### ⚠️ Security Practice: `textContent` vs. `innerHTML`
*   **`textContent`**: Treats all data as pure text strings. If you pass HTML tags (e.g. `"<script>alert(1)</script>"`), the browser displays the brackets as text on the screen without running them. This prevents Cross-Site Scripting (XSS) attacks.
*   **`innerHTML`**: Injects text as HTML code. If you pass user-inputted script strings to `innerHTML`, the browser compiles and runs the script, creating severe security vulnerabilities.

> [!IMPORTANT]
> Only use `innerHTML` when you hardcode static HTML strings yourself inside the code. Never pass user-supplied input strings directly to `innerHTML`.

---

## 3. Modifying Classes & Inline Styles

JavaScript can dynamically manipulate how elements look on the screen:

### A. The `classList` API
The safest and most performant way to change element states is by toggling CSS classes that are pre-defined in stylesheets.
*   `element.classList.add("class-name")`: Appends a class.
*   `element.classList.remove("class-name")`: Deletes a class.
*   `element.classList.toggle("class-name")`: Alternates a class (adds if missing, removes if present).
*   `element.classList.contains("class-name")`: Returns `true` if class is present.

### B. Inline Style Mutation
You can apply styles directly to the element's style attribute. Note that properties use camelCase naming (e.g. `background-color` becomes `backgroundColor`):
```javascript
element.style.opacity = "0.85";
element.style.transform = "rotate(45deg)";
element.style.backgroundColor = "#ff0000";
```

> [!NOTE]
> Setting styles via the `.style` object writes **inline styles** directly onto the HTML node. This has a high specificity score of `(1,0,0,0)`, which will override normal rules inside your CSS stylesheets.

---

## 4. Mounting & Removing Nodes from the DOM Tree

Nodes created in memory are invisible until they are inserted into the active DOM document tree.

```javascript
const parent = document.getElementById("card-grid");
const newCard = document.createElement("div");
const firstCard = parent.querySelector(".success-card");

// A. Append to the end of the parent container
parent.appendChild(newCard);

// B. Insert before a specific child (inserts newCard directly before firstCard)
parent.insertBefore(newCard, firstCard);

// C. Remove an element from the screen
newCard.remove();
```

*   **Learn how this API drives client-side templating**: **[[Concepts/Web Development/Dynamic DOM Injection|Dynamic DOM Injection]]**
*   **Learn how user actions trigger these DOM changes**: **[[Concepts/Languages/JavaScript/Events|JavaScript Events]]**
