# HTML: Fundamentals & DOM Tree Structure 📑

HyperText Markup Language (HTML) is the standard markup language used to structure documents in a web browser. It defines the skeleton of a web page by telling the browser what content (text, images, inputs) to display and how that content is nested.

---

## 1. What is an HTML Element?

An **element** is the individual building block of HTML. It is created by wrapping content inside tags.

```text
  ┌─ Start Tag (Opening Tag)
  │      ┌─ Attribute Name
  │      │     ┌─ Attribute Value
  │      ▼     ▼
  <p class="intro-text">Hello, World!</p>
  ▲                     ▲              ▲
  │                     │              └─ End Tag (Closing Tag)
  │                     └─ Content
  └─ Entire HTML Element
```

### Components of an Element:
1.  **Start Tag (`<tagname>`)**: Marks the beginning of the element.
2.  **End Tag (`</tagname>`)**: Marks the end of the element (uses a forward slash).
3.  **Content**: The actual data (text or other HTML elements) placed inside the tags.
4.  **Attributes**: Key-value metadata declared inside the start tag (e.g. `class="intro-text"`). Attributes alter the element's behavior, style, or accessibility.
5.  **Self-Closing Tags**: Some elements do not have separate end tags because they do not wrap content. They are self-contained (e.g., `<img src="photo.jpg">`, `<input type="text">`, `<br>`).

---

## 2. Parent-Child Nesting Relationships

HTML is structured by nesting elements inside one another. Nesting establishes a strict family hierarchy:

```html
<main class="content-container">
    <h1>MoveRx Specialties</h1>
    <p>We provide <strong>corrective exercise</strong> training.</p>
</main>
```

In this code block:
*   `<main>` is the **Parent** of `<h1>` and `<p>`.
*   `<h1>` and `<p>` are **Children** of `<main>`, and are **Siblings** to each other.
*   `<strong>` is a **Child** of `<p>`, and a **Descendant** of `<main>`.

> [!IMPORTANT]
> Tags must be closed in the exact reverse order of how they were opened.
> *   **Correct**: `<p>Hello <strong>World</strong></p>`
> *   **Incorrect**: `<p>Hello <strong>World</p></strong>` (overlapping tags cause parsing bugs)

---

## 3. The Document Object Model (DOM) Tree

When a browser loads an HTML file, it does not display the source text file directly. Instead, the browser's HTML parser reads the text, creates JavaScript-accessible representations of each element, and structures them into a logical tree called the **Document Object Model (DOM)**.

*   Every HTML element, attribute, and text snippet becomes a **Node** in this tree.
*   **The Document Node** is the root of the entire tree.

### Visualizing the DOM Tree
For the nested HTML example above, the browser constructs the following DOM tree:

```text
                 [document] (Root)
                     │
                  [html]
                     │
                  [body]
                     │
                  [main] (Parent Element Node)
           ┌─────────┴─────────┐
           ▼                   ▼
     [h1] Node            [p] Node (Parent Element Node)
           │               ┌───┴───────────────┐
           ▼               ▼                   ▼
    "MoveRx..."     "We provide "        [strong] Node
    (Text Node)      (Text Node)               │
                                               ▼
                                      "corrective exercise"
                                          (Text Node)
```

### Why does this matter?
JavaScript cannot read or edit static HTML text directly. Instead, JavaScript reads and modifies the **DOM Tree** in memory. When you call JavaScript APIs (like `document.createElement()`), you are adding nodes directly to this memory tree. The browser immediately detects tree changes and repaints the screen.

*   **Read more on how JavaScript interacts with this structure**: **[[Concepts/Languages/JavaScript/DOM API|JavaScript DOM API]]**
*   **Learn how the browser loads and parses HTML files**: **[[Concepts/Languages/HTML/Document Lifecycle|HTML Document Lifecycle]]**
