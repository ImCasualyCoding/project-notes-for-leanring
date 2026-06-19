# JavaScript: Event Handling & Scroll Optimization 🎡

Events are actions or occurrences that happen inside the browser window (such as clicks, scrolls, keystrokes, or window resizing). JavaScript uses event handlers to react to these actions dynamically.

This note documents event bindings, event propagation (bubbling), form control, and scroll optimization.

---

## 1. Listening for Events

To handle an event, we attach an event listener to a DOM node using `element.addEventListener()`.

```javascript
const button = document.querySelector(".btn-primary");

// Named handler function
function handleButtonClick(event) {
    console.log("Button clicked!", event);
}

// Bind listener: Event type | Handler function | Options (Optional)
button.addEventListener("click", handleButtonClick);
```

### The Event Object (`e` / `event`)
When an event fires, the browser automatically passes an **Event Object** to the callback function. This object contains metadata about the occurrence:
*   `event.type`: The type of event (e.g. `"click"`, `"scroll"`).
*   `event.target`: The exact DOM element that initiated the event.
*   `event.preventDefault()`: Cancels the browser's default behavior for this action. For example, it prevents HTML forms from performing page refreshes on submission, or links from executing instant navigations.

```javascript
const form = document.querySelector("form");
form.addEventListener("submit", (e) => {
    e.preventDefault(); // Prevents page reload on form submit
    console.log("Form submission intercepted. Handling via custom script.");
});
```

---

## 2. Event Propagation: Bubbling & Capturing

When an event fires on an element that is nested deep inside other parents, the event propagates through the DOM tree. This sequence occurs in three phases:

```text
  1. Capture Phase ──────> [document] ─> [body] ─> [div] ────────┐
                                                                 ▼
  2. Target Phase  ────────────────────────────────────────> [button] (Event Target)
                                                                 │
  3. Bubbling Phase <───── [document] <─ [body] <─ [div] <───────┘
```

1.  **Capturing Phase**: The event trickles down from the root `document` node down to the targeted element.
2.  **Target Phase**: The event fires on the target element itself.
3.  **Bubbling Phase (Default)**: The event bubbles back up from the target element, through its parents, all the way to the root `document`.

### Halting Bubbling
If you click a button inside a card, and both the button and card have click listeners, clicking the button triggers both handlers. To prevent parent handlers from firing, call `event.stopPropagation()`:

```javascript
button.addEventListener("click", (e) => {
    e.stopPropagation(); // Event bubbles no further. Card click handler is bypassed.
    console.log("Button action processed.");
});
```

---

## 3. Throttling Scroll Events

Scroll events fire rapidly (often 60+ times per second). If your scroll event handler runs complex DOM transformations (like moving elements or changing opacity), it blocks the browser's main execution thread, causing severe lag and visual stuttering.

To optimize performance, we throttle calculations using the **Ticking flag pattern**:

```javascript
let isTicking = false;

window.addEventListener("scroll", () => {
    if (!isTicking) {
        // requestAnimationFrame schedule callback before next paint
        window.requestAnimationFrame(() => {
            performLayoutUpdates(); // Run heavy layout updates here
            isTicking = false;     // Reset gate lock
        });
        isTicking = true; // Lock gate
    }
});
```

*   **Learn the browser paint cycles and scheduler**: **[[Concepts/Languages/JavaScript/Concurrency & Renders|JavaScript Concurrency & Renders]]**

---

## ⚡ Passive Event Listeners

By default, when you scroll a page containing a scroll event handler, the browser's main layout thread halts page rendering for a split-second. It does this to check if the scroll event listener calls `event.preventDefault()` to cancel the scroll action.

For scroll and touch interactions, we declare `{ passive: true }` in the options parameter. This tells the browser that this listener will never cancel the action, allowing the browser to perform page scrolling instantly on a separate background thread:

```javascript
// Optimized Scroll Hook
window.addEventListener("scroll", handleScroll, { passive: true });
```

*   **Learn how this optimization is implemented in visual components**: **[[Concepts/Web Development/Scroll Animations|Scroll Animations]]**
