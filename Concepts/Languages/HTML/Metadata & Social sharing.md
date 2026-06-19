# HTML: Metadata, Viewports, & Open Graph Social Previews 📱

Metadata refers to information about the web page that is processed by browsers, search engine crawlers, and social media platforms, rather than being rendered visually to the user.

This note documents the configurations for mobile device scaling and social share card pre-renders (Open Graph).

---

## 1. Responsive Layout: The Viewport Meta Tag

By default, mobile devices render websites on a wide virtual desktop screen (usually 980px wide) and shrink the layout to fit the tiny screen, making text unreadably small.

To prevent this, every modern page must include a viewport meta tag inside the `<head>` block:
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0">
```

### Breakdown of Parameters:
*   `width=device-width`: Tells the browser to set the width of the page's virtual viewport to match the physical pixel width of the device screen (e.g., 390px on an iPhone 12).
*   `initial-scale=1.0`: Sets the initial zoom level when the page first loads.
*   `minimum-scale=1.0`: Prevents automated layout shifts on specific orientations, ensuring a stable starting rendering grid.

*   **Learn how responsive layouts adapt using styling selectors**: **[[Concepts/Languages/CSS/Layouts (Bento & Grid)|CSS Layouts (Grid & Flexbox)]]**

---

## 2. Link Previews & The Open Graph (OG) Protocol

When you send a website link through chat applications (Slack, Discord, iMessage, WhatsApp) or post it on social platforms (Facebook, Twitter, LinkedIn), a visual summary preview card appears.

```text
  ┌────────────────────────────────────────────────────────┐
  │  Active Horizons FL                                    │ ◄── og:title
  │  Discover Mollie's professional corrective exercise...│ ◄── og:description
  │  ┌──────────────────────────────────────────────────┐  │
  │  │                                                  │  │
  │  │                                                  │  │ ◄── og:image
  │  │                     [IMAGE]                      │  │
  │  │                                                  │  │
  │  └──────────────────────────────────────────────────┘  │
  └────────────────────────────────────────────────────────┘
```

This preview is built using the **Open Graph Protocol**, a standard created by Facebook that uses structured `<meta>` elements inside the `<head>` section.

### Core Open Graph Tags:
```html
<!-- The title of the page as it should appear in the share card -->
<meta property="og:title" content="MoveRx | Precision Mobility & Rehab with Mollie">

<!-- A brief, engaging subtitle description (typically 1-2 sentences) -->
<meta property="og:description" content="South Florida's premier corrective exercise specialist. Safe, evidence-based post-rehab recoveries.">

<!-- The URL to the image that will represent your page (Must be absolute, e.g. https://domain.com/img.jpg) -->
<meta property="og:image" content="https://activehorizonsfl.com/assets/images/hero/mollies%20brother.jpg">

<!-- The canonical URL of your website -->
<meta property="og:url" content="https://activehorizonsfl.com/index.html">

<!-- The type of content (e.g., website, article, video, profile) -->
<meta property="og:type" content="website">
```

---

## 🐦 Twitter Card Metadata

Twitter uses its own variant of metadata tags. If they are absent, Twitter falls back to Open Graph tags, but specifying them provides more precise layout controls:

```html
<!-- Card layout style: summary (small thumbnail) or summary_large_image (giant card) -->
<meta name="twitter:card" content="summary_large_image">

<!-- Page Title -->
<meta name="twitter:title" content="MoveRx | Precision Mobility & Rehab">

<!-- Description text -->
<meta name="twitter:description" content="Safe, evidence-based post-rehab functional recoveries.">

<!-- Image URL -->
<meta name="twitter:image" content="https://activehorizonsfl.com/assets/images/hero/mollies%20brother.jpg">
```

---

## 🛠️ Application in MoveRx

In the early stages of building Mollie's website, sending links to clients resulted in automatic, random page text scrapes and empty images because the Open Graph headers were missing. 

To fix this, we added dedicated tags inside [the-mission.html](file:///e:/repositories/mollies-page-v2/the-mission.html#L11-L15):
```html
<!-- Open Graph (OG) Social Sharing Previews -->
<meta property="og:title" content="The Mission | MoveRx | Specialties & Credentials">
<meta property="og:description" content="Discover Mollie's professional physical accreditations, posture correction methodologies, post-rehab joint training, and aqua fitness instructions.">
<meta property="og:image" content="assets/images/hero/mollies%20brother.jpg">
<meta property="og:type" content="website">
```

### Crucial Lessons:
1.  **Image Paths**: Social platform crawler crawlers do not run inside the client's browser. They read the HTML directly. If you supply a local path like `images/pic.jpg` to a website that is not deployed, the share preview card will fail to fetch the image. When deploying, always replace local preview paths with absolute URLs (`https://activehorizonsfl.com/assets/images/...`).
2.  **File Naming and Caching**: If you change the `og:image` but keep the same name, social media servers (which cache preview cards aggressively) will not update the image. To force a preview card refresh, change the filename of the target image (e.g. `preview-v2.jpg`) and update the tag content.
