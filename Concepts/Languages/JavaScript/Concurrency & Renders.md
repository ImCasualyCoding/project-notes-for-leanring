# JavaScript: The Event Loop & requestAnimationFrame rendering 🔄

JavaScript is a **single-threaded** language, meaning it can only execute one line of code at a time. However, web browsers can perform multiple background operations simultaneously (like downloading files or processing timers).

This note documents how JavaScript coordinates asynchronous tasks using the **Event Loop** and schedules smooth rendering using **`requestAnimationFrame`**.

---

## 1. The Concurrency Model (The Event Loop)

To run asynchronous tasks without freezing the browser interface, JavaScript utilizes a concurrency model composed of four core systems:

```text
 ┌────────────────────────┐         ┌────────────────────────┐
 │       CALL STACK       │         │        WEB APIs        │
 │                        │         │                        │
 │  [calculateSway()]     │         │   [DOM Event: scroll]  │
 │  [updateLeaves()]      │ ──────> │   [Network Fetch]      │
 │  [scrollListener()]    │         │   [Timer: 100ms]       │
 └────────────────────────┘         └────────────────────────┘
             ▲                                   │
             │ (Pushes task when Stack is empty) │ (Task completes, sends callback)
             │                                   ▼
 ┌────────────────────────┐         ┌────────────────────────┐
 │       EVENT LOOP       │ <────── │     CALLBACK QUEUE     │
 └────────────────────────┘         │                        │
                                    │   [renderVisuals]      │
                                    └────────────────────────┘
```

### A. The Call Stack
A Last-In-First-Out (LIFO) stack that tracks what function is currently running. When a function is called, it is pushed onto the stack. When it returns, it is popped off.

### B. Web APIs (Browser APIs)
Background threads provided by the browser (not the JS engine). They handle timers (`setTimeout`), network requests (`fetch`), and event listeners. They run in the background without blocking the Call Stack.

### C. The Callback Queue (Task Queue)
A First-In-First-Out (FIFO) queue holding callbacks that have finished running in the Web API thread and are waiting to be executed on the main JS thread.

### D. The Event Loop
The manager that constantly monitors the Call Stack. 
*   If the Call Stack is **not empty**, it waits.
*   The moment the Call Stack becomes **completely empty**, the Event Loop takes the first callback task from the Callback Queue and pushes it onto the Call Stack to execute.

---

## 2. Why `setTimeout` is Unreliable for Animations

A common novice mistake is scheduling animation frames using timers:
```javascript
// Triggers every 16ms to approximate 60 frames per second
setInterval(renderVisuals, 16.6); 
```

### The Problem:
`setInterval` does not guarantee execution times. 
1.  If the timer completes, but the Call Stack is busy doing heavy math calculations, the callback sits in the Callback Queue.
2.  It executes only after the Call Stack clears.
3.  As a result, frames are skipped, causing stuttering animations.
4.  Timers run independently of the screen refresh rate, causing visual tears where frames are painted mid-refresh.

---

## 3. The Browser Render Cycle & `requestAnimationFrame`

Right before a browser repaints the screen, it runs a dedicated **Render Pipeline**:

```text
  [JS Execution] ─> [Run requestAnimationFrame] ─> [Style Calcs] ─> [Layout] ─> [Paint] ─> [Composite]
```

To align animations with this pipeline, browsers provide the `requestAnimationFrame` (rAF) API.

### What `requestAnimationFrame(callback)` does:
1.  It tells the browser that you want to perform an animation update.
2.  It schedules the callback to run **right before** the browser performs its next paint operation.
3.  It automatically matches the user's screen refresh rate (e.g. 60Hz, 90Hz, 120Hz).
4.  If the page tab is hidden or minimized, the browser pauses rAF calls automatically, saving CPU power and battery life.

---

## 4. Implementation Example: Scroll Sway throttling

Here is how the Event Loop and `requestAnimationFrame` cooperate to render smooth scroll animations:

```javascript
let isTicking = false;

function updateSways() {
  // 3. Executed right before the paint step.
  // Performs calculations and DOM transforms.
  const cards = document.querySelectorAll(".success-card");
  cards.forEach(card => {
    card.style.transform = "rotate(5deg)";
  });
  
  isTicking = false; // 4. Reset the gate lock
}

window.addEventListener("scroll", () => {
  // 1. Scroll event fires.
  if (!isTicking) {
    // 2. Schedule updateSways to run at the next repaint.
    // The main thread is released immediately to scroll the page.
    window.requestAnimationFrame(updateSways);
    isTicking = true; // Lock the gate
  }
}, { passive: true }); // Optimized scroll path
```

*   **Learn how this scroll loop integrates with CSS animations**: **[[Concepts/Languages/CSS/Animations & Transforms|CSS Animations & Transforms]]**
*   **Learn how viewport coordinates are computed in this loop**: **[[Concepts/Web Development/Scroll Animations|Scroll Animations]]**
