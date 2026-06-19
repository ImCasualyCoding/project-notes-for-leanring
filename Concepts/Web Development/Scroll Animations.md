# Scroll-Bound Animations & Performance Optimization 🎡

Scroll-bound animations trigger changes in styles (like opacity, rotation, or scale) directly linked to the user's viewport scroll progress. When built incorrectly, scroll listeners cause significant lag and visual stuttering ("scroll jank").

This note explains how to track viewport coordinates, apply smooth easing math, and build a high-performance scroll rendering engine.

---

## 1. Viewport Calculations

To trigger animations when an element is visible on the screen, you must locate its position relative to the current viewport bounds using `Element.getBoundingClientRect()`.

### key Properties:
*   `rect.top`: Distance from the top of the viewport to the top of the element.
*   `rect.height`: Total height of the element box.
*   `window.innerHeight`: The vertical height of the browser window.

```javascript
// Calculate center coordinates
const viewportCenter = window.innerHeight / 2;
const rect = element.getBoundingClientRect();
const elementCenter = rect.top + (rect.height / 2);

// Calculate absolute distance from element center to screen center
const distance = Math.abs(elementCenter - viewportCenter);
```

---

## 2. Mathematical Easing: The Sine Wave 📈

If you map coordinates directly to transforms, the movement will feel linear and mechanical. To make elements slide in organically, we use mathematical easing.

For MoveRx, we used a **Sinusoidal Ease-Out** curve:
$$\text{ease} = \sin\left(\text{progress} \times \frac{\pi}{2}\right)$$

In JavaScript:
```javascript
const ease = Math.sin(progress * Math.PI / 2);
```

### Why use `Math.sin`?
1.  **Linear Progress** (`progress` from `0` to `1`): The value rises at a constant speed.
2.  **Sinusoidal Progress** (`ease` from `0` to `1`): The value rises quickly at first, then decelerates smoothly as it approaches `1`.
3.  **Result**: Natural, soft dampening when the element settles into place on the screen.

```text
1.0 |                   .---
    |                .-/
    |              ./
    |            ./
    |          ./
0.5 |        ./
    |      ./
    |    ./
    |  ./
0.0 |./_____________________
   0.0          0.5         1.0 (Progress)
```

---

## 3. Scroll Performance Optimization

Scroll events fire rapidly (up to 60+ times per second). If your scroll handler does heavy DOM calculations, it blocks the browser's paint loop, dropping frame rates.

We solve this using three performance patterns:

### A. Throttling with `requestAnimationFrame`
`requestAnimationFrame` (rAF) schedules visual updates to run right before the browser performs its next paint screen render, aligning code execution with the monitor's refresh rate. We use a boolean gate (`ticking`) to ensure multiple scroll events do not trigger multiple computations within the same frame.

### B. Passive Event Listeners
By default, the browser waits for a scroll event listener to execute to see if it cancels the scroll (via `event.preventDefault()`). This causes scrolling delays. Specifying `{ passive: true }` tells the browser that this listener will never cancel the scroll, allowing the page to scroll immediately on a separate background thread.

### C. Restricting Calculations
Only compute values when the element is actually close to the screen (i.e. `distance < maxDistance`). If it is far off-screen, exit early and bypass DOM styling.

---

## 4. Standard Optimized Scroll Pattern

Here is a copy-pasteable boiler template for high-performance scroll tracking:

```javascript
document.addEventListener("DOMContentLoaded", () => {
  const animatedElements = document.querySelectorAll(".scroll-target");

  function renderVisuals() {
    const viewportCenter = window.innerHeight / 2;
    const maxTriggerRange = window.innerHeight * 0.7; // Vertical range of action

    animatedElements.forEach(el => {
      const rect = el.getBoundingClientRect();
      const elCenter = rect.top + rect.height / 2;
      const distance = Math.abs(elCenter - viewportCenter);

      if (distance < maxTriggerRange) {
        // 1. Calculate linear progress (0 at edge of trigger, 1 at center of viewport)
        const progress = 1 - (distance / maxTriggerRange);
        
        // 2. Apply Sinusoidal ease-out dampening
        const ease = Math.sin(progress * Math.PI / 2);
        
        // 3. Apply style transforms
        el.style.opacity = ease;
        el.style.transform = `translateY(${(1 - ease) * 50}px)`;
      } else {
        // Reset properties if element is fully off-screen
        el.style.opacity = 0;
        el.style.transform = "translateY(50px)";
      }
    });
  }

  // Throttling mechanism
  let isTicking = false;
  window.addEventListener("scroll", () => {
    if (!isTicking) {
      window.requestAnimationFrame(() => {
        renderVisuals();
        isTicking = false; // Reset gate
      });
      isTicking = true; // Lock gate
    }
  }, { passive: true }); // Optimized scroll path

  // Initial trigger run
  renderVisuals();
  
  // Re-calculate values if viewport size changes
  window.addEventListener("resize", renderVisuals, { passive: true });
});
```
