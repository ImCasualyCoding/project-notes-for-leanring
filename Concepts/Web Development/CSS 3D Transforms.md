# CSS 3D Transforms & Dynamic Tilt Grids 📐

CSS transforms allow you to manipulate elements in two-dimensional (2D) or three-dimensional (3D) space. By configuring a 3D rendering context, you can rotate, scale, skew, and translate elements along the X, Y, and Z axes.

This note documents the theory, math, and code behind creating a **JavaScript-free 3D tilt card** using perspective and hover grids.

---

## 1. Core 3D CSS Properties

To render elements in 3D space, you must configure two crucial properties on parent containers:

### A. `perspective`
The `perspective` property determines the distance between the user’s viewport and the Z=0 plane. It acts as the camera focal length.
*   **Smaller values** (e.g. `200px`) produce extreme, dramatic perspective distortions.
*   **Larger values** (e.g. `1000px` or `2000px`) produce a subtle, orthographic-like rendering.
*   Must be defined on a parent container to influence its 3D transformed children.

```css
.parent-container {
  perspective: 800px; /* camera distance from the card */
}
```

### B. `transform-style`
By default, nested child elements are flattened into the 2D plane of their parent. To allow children to position themselves in their own 3D layers, set this property to `preserve-3d`.

```css
.card-canvas {
  transform-style: preserve-3d;
}
```

---

## 2. Mathematical Rotations in 3D Space

3D rotations occur around the three primary axes. Note the behavior of rotation directions:
*   `rotateX(a)`: Rotates the element around the horizontal X-axis (tilts the card forward or backward).
*   `rotateY(a)`: Rotates the element around the vertical Y-axis (turns the card left or right).
*   `rotateZ(a)`: Rotates the element around the depth Z-axis (spins the card clockwise/counter-clockwise, equivalent to a 2D rotation).

```text
       Y-Axis (rotateY)
             ^
             |
             |
   ----------|-----------> X-Axis (rotateX)
            /|
           / |
          v  
       Z-Axis (rotateZ)
```

> [!NOTE]
> Positive rotations are calculated using the **Right-Hand Rule**. If you point your right thumb along the positive axis direction, your curling fingers point in the direction of positive rotation.

---

## 3. Creating a Pure-CSS Tracker Grid

Usually, interactive mouse tilts require listening to `mousemove` events in JavaScript and calculating percentages. We can achieve this in pure CSS by using **Grid Trackers** and the **General Sibling Combinator (`~`)**.

### How it works:
1.  Overlay the card with a grid of transparent items (the trackers).
2.  Use absolute positioning and percentage dimensions (e.g., a 3x3 grid with `width: 33.3%` or a 5x5 grid with `width: 20%`).
3.  When a tracker is hovered, use the `~` combinator to target the card content sibling and apply rotations.

### CSS Sibling Architecture
In HTML, trackers must be declared **before** the card content in the DOM tree, because the CSS sibling selector (`~`) can only select elements that appear *after* the trigger in the HTML source.

```html
<div class="card-wrapper">
  <!-- Trackers appear first -->
  <div class="tracker tr-1"></div>
  <div class="tracker tr-2"></div>
  <div class="tracker tr-3"></div>
  
  <!-- Content to transform appears last -->
  <div class="card-content">
      <h3>Hover Me!</h3>
  </div>
</div>
```

```css
/* Card Setup */
.card-wrapper {
  position: relative;
  width: 300px;
  height: 400px;
  perspective: 1000px;
}

.card-content {
  width: 100%;
  height: 100%;
  transition: transform 0.5s cubic-bezier(0.25, 1, 0.5, 1);
  transform-style: preserve-3d;
}

/* Tracker styling (transparent) */
.tracker {
  position: absolute;
  width: 33.33%;
  height: 33.33%;
  z-index: 10; /* sits on top of content */
}

/* Positioning Trackers (3x3 Grid Example) */
.tr-1 { left: 0%;    top: 0%; }
.tr-2 { left: 33.3%; top: 0%; }
.tr-3 { left: 66.6%; top: 0%; }
.tr-4 { left: 0%;    top: 33.3%; }
.tr-5 { left: 33.3%; top: 33.3%; } /* Center */
.tr-6 { left: 66.6%; top: 33.3%; }
.tr-7 { left: 0%;    top: 66.6%; }
.tr-8 { left: 33.3%; top: 66.6%; }
.tr-9 { left: 66.6%; top: 66.6%; }

/* Applying 3D Tilts */
/* Top-Left Hover -> Tilts card up and left */
.tr-1:hover ~ .card-content {
  transform: rotateX(10deg) rotateY(-10deg);
}
/* Top-Right Hover -> Tilts card up and right */
.tr-3:hover ~ .card-content {
  transform: rotateX(10deg) rotateY(10deg);
}
/* Bottom-Left Hover -> Tilts card down and left */
.tr-7:hover ~ .card-content {
  transform: rotateX(-10deg) rotateY(-10deg);
}
/* Bottom-Right Hover -> Tilts card down and right */
.tr-9:hover ~ .card-content {
  transform: rotateX(-10deg) rotateY(10deg);
}
/* Center Hover (and default state) */
.tr-5:hover ~ .card-content {
  transform: rotateX(0) rotateY(0);
}
```

---

## 🚀 Key Advantages of Pure CSS 3D Tilts
1.  **Performance**: CSS transformations are hardware-accelerated by the GPU, resulting in a smooth 60fps refresh rate.
2.  **No Layout Thrashing**: Unlike JavaScript `mousemove` calculations, CSS changes do not cause browser reflows.
3.  **Clean Code**: Avoids writing complex canvas calculations or event listeners in script files.
