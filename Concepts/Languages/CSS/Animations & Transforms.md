# CSS Animations: Transforms, Transitions, & GPU Acceleration 🎬

CSS animations bring websites to life by creating visual transitions between different style states. For a novice, animating layouts can easily trigger lag and high CPU consumption. 

This note documents transitions, keyframe animations, 2D transforms, and **GPU hardware acceleration**.

---

## 1. CSS Transitions

A transition allows you to change property values smoothly (from an initial state to a target state) over a defined duration.

```css
.button {
  background-color: #0d9488;
  /* property | duration | timing-function | delay */
  transition: background-color 0.3s cubic-bezier(0.25, 0.8, 0.25, 1) 0s;
}

.button:hover {
  background-color: #0f172a;
}
```

### Timing Functions:
*   `linear`: Constant speed throughout the transition.
*   `ease-in`: Starts slow, speeds up at the end.
*   `ease-out`: Starts fast, slows down at the end.
*   `cubic-bezier(x1, y1, x2, y2)`: A custom cubic bezier curve defining custom acceleration profiles.

---

## 2. CSS 2D Transforms

Transforms modify the coordinate space of an element. They can be animated without altering document flow layout metrics.
*   `translate(x, y)`: Moves the element from its starting position.
*   `scale(x, y)`: Resizes the element (e.g., `scale(1.05)` zooms the card by 5%).
*   `rotate(deg)`: Spins the element around its origin point.

---

## 3. The Browser Rendering Pipeline & GPU Acceleration 🚀

To understand why some animations lag while others run smoothly at 60fps, we must trace how the browser draws a web page on the screen:

```text
 [1. Parsing HTML/CSS] ─> [2. Style Calculations] ─> [3. Layout] ─> [4. Paint] ─> [5. Composite]
```

1.  **Layout**: Computes the geometry and coordinates of every box container on the page.
2.  **Paint**: Fills in the actual pixels for text, borders, shadows, and images.
3.  **Composite**: Layers are sent to the graphics processor (GPU) to draw on the screen.

### The Performance Trap: Reflows & Repaints
If you animate properties like `top`, `left`, `width`, or `height`, you force the browser to run **Layout** and **Paint** calculations on every frame. If the CPU cannot calculate these changes quickly enough, frames drop, causing stuttering.

### The GPU Shortcut: Compositor-Only Properties
Two properties skip the Layout and Paint steps entirely, running directly on the GPU's **Compositing** layer:
1.  **`transform`** (`translate`, `scale`, `rotate`)
2.  **`opacity`**

When you animate a card using `transform: translateY(-10px)`, the browser paints the card once onto a separate layer, sends it to the GPU, and lets the graphics card translate the pixels. This ensures hardware-accelerated, lag-free animations.

> [!TIP]
> You can force an element onto its own GPU compositor layer by using 3D transforms (like `translate3d(x, y, z)`) or by declaring `will-change: transform, opacity;` in CSS.

---

## 4. Keyframe Animations

While transitions run between two states (e.g., normal to hover), keyframes allow you to define multi-stage animations that run automatically.

### Code Example: Fade-In-Up Page Entry
```css
/* 1. Define the Keyframe Timeline */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(30px); /* Starts lower and invisible */
  }
  to {
    opacity: 1;
    transform: translateY(0);    /* Settles into normal position */
  }
}

/* 2. Apply the animation to classes */
.animate-card {
  animation-name: fadeInUp;
  animation-duration: 0.8s;
  animation-timing-function: cubic-bezier(0.25, 1, 0.5, 1);
  animation-fill-mode: forwards; /* Holds the "to" state once finished */
}

/* Creating staggered entry delays */
.delay-1 { animation-delay: 0.1s; }
.delay-2 { animation-delay: 0.2s; }
.delay-3 { animation-delay: 0.3s; }
```

*   **Learn how these animations are bound to viewport scroll events**: **[[Concepts/Web Development/Scroll Animations|Scroll Animations]]**
*   **Learn how 3D space transforms are computed**: **[[Concepts/Web Development/CSS 3D Transforms|CSS 3D Transforms]]**
