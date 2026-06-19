# MoveRx Website: Interactive Core Features 🌟

The premium aesthetic of the MoveRx website is defined by three custom interactive systems: scroll-animated decorative leaves, an interactive 3D tilt card, and a before/after image transition slider.

---

## 1. Scroll-Animated Leaves
Rather than static background patterns, the website features vector tropical leaves that sway into the viewport as the user scrolls.

*   **Implementation file**: `scroll-leaves.js` (linked in `index.html`)
*   **Concepts behind it**: **[[Concepts/Web Development/Scroll Animations|Scroll-Bound Animations]]**

### How it Works
The code calculates the distance between the center of each leaf container and the center of the viewport. If the leaf is within the **vertical range of influence** (65% of viewport height), it calculates scroll progress (0 to 1) and translates it into a smooth sinusoidal sway and slide-in offset.

### The Code Breakdown
```javascript
// vertical range of influence (65% of viewport height)
const maxDistance = window.innerHeight * 0.65; 

leaves.forEach(leaf => {
    const rect = leaf.getBoundingClientRect();
    const leafCenter = rect.top + rect.height / 2;
    const distance = Math.abs(leafCenter - viewportCenter);
    
    if (distance < maxDistance) {
        // Calculate progress (0 = edge, 1 = perfectly centered)
        const progress = 1 - (distance / maxDistance);
        
        // Easing function: Sinusoidal ease-out for organic sways
        const ease = Math.sin(progress * Math.PI / 2);
        
        const opacity = ease * 0.13; // Max 13% opacity for subtle visual decor
        const isLeft = leaf.classList.contains("leaf-left");
        
        // Sway calculation: from 10deg (hanging straight) to 55deg (reaching deep)
        const startAngle = 10;
        const endAngle = 55;
        const currentAngle = startAngle + ease * (endAngle - startAngle);
        const rotateDeg = isLeft ? currentAngle : -currentAngle;
        
        // Slide inward: from -60px (off-screen) to 0px (on-screen)
        const slideOffset = (1 - ease) * 60;
        const translateX = isLeft ? -slideOffset : slideOffset;
        
        // Apply properties to element
        leaf.style.opacity = opacity;
        leaf.style.transform = `translateX(${translateX}px) rotate(${rotateDeg}deg)`;
    } else {
        // Out of range - fully hidden and rotated off-screen
        const isLeft = leaf.classList.contains("leaf-left");
        leaf.style.opacity = 0;
        leaf.style.transform = `translateX(${isLeft ? -60 : 60}px) rotate(${isLeft ? 10 : -10}deg)`;
    }
});
```

---

## 2. Interactive 3D Tilt Spotlight Card
The featured spotlight card inside `testimonials.html` utilizes a pure CSS, high-performance 3D parallax tilt effect. It requires zero Javascript runtime overhead.

*   **Implementation file**: `testimonials.html` and `testimonials.css`
*   **Concepts behind it**: **[[Concepts/Web Development/CSS 3D Transforms|CSS 3D Transforms]]**

### How it Works
The card structure contains a `canvas-3d` element overlayed by a 5x5 grid of invisible tracker elements (`.tracker.tr-1` to `.tr-25`). 
When the user hovers over a specific tracker in the grid, CSS selector nesting uses that specific tracker to apply unique `transform: rotateX()` and `rotateY()` styles to the underlying card content.

```html
<div class="card-container-3d noselect">
    <div class="canvas-3d">
        <!-- 25 trackers on top -->
        <div class="tracker tr-1"></div>
        <div class="tracker tr-2"></div>
        <!-- ... tr-3 to tr-24 ... -->
        <div class="tracker tr-25"></div>
        
        <!-- Card content visible underneath -->
        <div class="card-content-3d">
            <h2>The MoveRx Mission</h2>
            <!-- Stats & text -->
        </div>
    </div>
</div>
```

### The CSS Selector Layout
CSS handles coordinates for the 25 divisions. For example, hovering over the top-left area (`tr-1`) tilts the card up and left. Hovering over the bottom-right (`tr-25`) tilts it down and right.
```css
/* Enable 3D Depth */
.card-container-3d {
  position: relative;
  perspective: 800px; /* Activates 3D space */
}

.canvas-3d {
  transform-style: preserve-3d;
  transition: transform 0.5s ease;
}

/* Grid of trackers overlaying the card */
.tracker {
  position: absolute;
  width: 20%;
  height: 20%;
  z-index: 100;
}

/* Grid layout mapping */
.tr-1  { left: 0%;   top: 0%; }
.tr-2  { left: 20%;  top: 0%; }
.tr-5  { left: 80%;  top: 0%; }
.tr-21 { left: 0%;   top: 80%; }
.tr-25 { left: 80%;  top: 80%; }

/* Applying 3D Rotations on Hover */
.tr-1:hover  ~ .card-content-3d { transform: rotateX(15deg)  rotateY(-15deg); }
.tr-5:hover  ~ .card-content-3d { transform: rotateX(15deg)  rotateY(15deg);  }
.tr-21:hover ~ .card-content-3d { transform: rotateX(-15deg) rotateY(-15deg); }
.tr-25:hover ~ .card-content-3d { transform: rotateX(-15deg) rotateY(15deg);  }
```

---

## 3. Before-and-After Interactive Image Slider
To showcase real physical improvements (like posture realignment and elderly range of motion), we created interactive before/after panels.

### UI Interaction Flow
Each client card displays a before-and-after photo box.
1.  By default, the `.img-before` is visible, and `.img-after` is hidden.
2.  Upon a tap or mouse click, the `.image-transition-box` toggles a CSS class: `.show-after`.
3.  The `.img-after` transitions into view with a smooth opacity fade (`transition: opacity 0.5s ease-in-out`).

### Structure
```html
<div class="image-transition-box" tabindex="0">
    <img src="images/posture_correction/judy_3.jpg" class="img-before" alt="Before">
    <img src="images/posture_correction/judyn_2.jpg" class="img-after" alt="After Progress">
    <div class="status-badge badge-before">Before</div>
    <div class="status-badge badge-after">After Progress</div>
    <div class="interaction-hint"><span class="hint-text">Tap to reveal</span></div>
</div>
```

```javascript
document.addEventListener("DOMContentLoaded", () => {
    const transitionBoxes = document.querySelectorAll(".image-transition-box");
    transitionBoxes.forEach(box => {
        box.addEventListener("click", function() {
            this.classList.toggle("show-after");
            const hintText = this.querySelector(".hint-text");
            hintText.textContent = this.classList.contains("show-after") 
                ? "Tap to see Before" 
                : "Tap to see After";
        });
    });
});
```
