# MoveRx Website: Project Overview 🚀

The **MoveRx** website is a premium, high-craft, multi-page portfolio built for **Mollie Wittenkeller**, a South Florida-based Corrective Exercise Specialist & Movement Scientist. The primary objective of this project was to establish a digital presence that feels clinical, authoritative, and premium, completely avoiding generic "AI slop" while introducing subtle, high-end interactive elements that engage and wow users.

---

## 📋 Project Metadata
*   **Client**: Mollie Wittenkeller (MoveRx)
*   **Service Area**: Palm Beach County, FL (Delray Beach, Palm Beach, Boynton Beach, Boca Raton)
*   **Core Audience**: Older adults recovering from surgeries (joint replacements), cancer survivors rebuilding endurance, and individuals managing chronic joint pain or kinetic issues.
*   **Key Design Theme**: South Florida tropical minimalism, luxury clinical rehab (Teal, Dark Slate, Muted Gold, Soft Off-White).

---

## 🎨 Design Philosophy & Brand Identity

### 1. Reclaiming Trust vs. "AI Slop"
To avoid looking like a template-generated website, we custom-built the layouts and spacing using pure CSS design tokens.
*   **Typography**: We combined **Playfair Display** (an elegant, intellectual serif for headings) with **Outfit** (a crisp, geometric sans-serif for reading legibility).
*   **Color Palette**: Sleek dark slate and charcoal backdrops are accented by vibrant medical-teal glows (`rgba(20, 184, 166, 0.15)`) and sand-colored muted golds, creating a high-end clubhouse feel instead of a sterile hospital look.

### 2. South Florida Tropical Foliage
Since Mollie operates in South Florida's coastal communities, we integrated tropical foliage sways. These leaves are not static backgrounds; they are vector SVGs that dynamically sway and slide onto the screen based on the user's scroll progress (see [[Projects/MoveRx Website/Interactive Features#1-scroll-animated-leaves|Scroll-Animated Leaves]] for details).

---

## 🛠️ Project Structure & Pages

The project went through two major iterations:
1.  **Version 1 (`mollies-webpage`)**: A clean, static-page implementation where content was written directly in HTML.
2.  **Version 2 (`mollies-page-v2`)**: A fully decoupled, dynamic architecture where all textual content, services, stats, and configurations are moved into separate data files (e.g., `mission-data.js` and `testimonials-data.js`) and injected dynamically by rendering engines (`mission.js`, `testimonials.js`). (See [[Projects/MoveRx Website/Architecture|Architecture & Organization]] for details).

### Pages Included:
*   **Landing Page (`index.html`)**: Features the MoveRx clinical positioning, the interactive services carousel, before/after client progress scans, and a contact form.
*   **Credentials & Mission (`credibility.html` / `the-mission.html`)**: Details Mollie's biomechanical screening methodology, training progression frameworks, and digital certification marquee.
*   **Success & Testimonials (`testimonials.html`)**: Reviews from actual clients who achieved life-altering results, coupled with a 3D tilt-interactive spotlight card.

---

## 🌟 Interactive Core Features
The website sets itself apart through customized interactive features. Click below for complete source code, math, and layout breakdowns of these features:

1.  **[[Projects/MoveRx Website/Interactive Features#1-scroll-animated-leaves|Scroll-Animated Tropical Leaves]]**: Decorative SVG foliage sways and slides in as you scroll, using throttled scroll listeners.
2.  **[[Projects/MoveRx Website/Interactive Features#2-interactive-3d-tilt-spotlight-card|Interactive 3D Tilt Spotlight Card]]**: A 3D parallax hover effect built with a 25-tracker grid in pure CSS (no JS required).
3.  **[[Projects/MoveRx Website/Interactive Features#3-before-and-after-interactive-image-slider|Before-and-After Image Slider]]**: Tap-to-reveal comparison cards for client physical transformations (e.g. posture correction, elderly mobility).

---

## 📊 Client Success Records & Testimonials
The content of the site highlights real transformations:
*   **John C. (Delray Beach, FL)**: Rebuilt endurance as a cancer survivor; recovered full range of movement in both shoulders after multiple doctors said it was impossible; gained 25 lbs of solid muscle over 6.5 years.
*   **Cathy B. (Palm Beach, FL)**: Traffic accident survivor managing arthritis and fibromyalgia; achieved pain relief that medication couldn't provide.
*   **Benjamin P. (Boynton Beach, FL)**: 26-year veteran paramedic, lieutenant firefighter, and exercise physiologist who endorsed Mollie's clinical expertise.
*   **Coach Conroy W. (Zion Lutheran School)**: 10+ year trainer of NFL and collegiate athletes who validated Mollie's strength coaching knowledge.
