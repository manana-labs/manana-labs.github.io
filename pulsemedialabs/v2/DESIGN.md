# Design System Document

## 1. Overview & Creative North Star: "The Architectural Pulse"

This design system is engineered to transition from a static informational layout to a high-conversion, sales-focused digital experience. We are moving away from the "academic list" aesthetic of the original site toward a **Creative North Star** we call **"The Architectural Pulse."**

"The Architectural Pulse" treats the UI as a living, three-dimensional environment. We replace rigid 1px borders and generic lists with deep tonal layering, intentional asymmetry, and sophisticated editorial typography. By utilizing high-contrast accents against a warm, neutral foundation, we create a "Premium SaaS" atmosphere that feels both authoritative and innovative. The goal is to guide the user’s eye through a narrative flow, using depth and scale to signal importance and drive conversion.

---

## 2. Colors: Tonal Depth & Soul

The palette is rooted in deep teals and warm, paper-like neutrals. We avoid "flat" design by using a hierarchy of surfaces that simulate physical layers.

### The "No-Line" Rule
**Explicit Instruction:** Use of 1px solid lines for sectioning is strictly prohibited. Boundaries must be defined through background shifts. For example, a `surface-container-low` (#f7f3e9) section should sit directly against a `surface` (#fdf9ef) background to create a soft edge.

### Surface Hierarchy & Nesting
Treat the UI as a series of nested sheets. 
- **Base Level:** `surface` (#fdf9ef)
- **Primary Content Blocks:** `surface-container` (#f1eee4)
- **Floating/Elevated Elements:** `surface-container-lowest` (#ffffff) for maximum "lift."

### Glass & Gradient Rule
To ensure a custom, premium feel:
- **Glassmorphism:** For floating navigation or modal overlays, use `surface` at 80% opacity with a `20px` backdrop-blur. 
- **Signature Textures:** For high-impact areas like Hero backgrounds or Primary CTAs, use a subtle linear gradient from `primary` (#01151c) to `primary-container` (#162a31). This adds a "visual soul" that flat hex codes cannot replicate.

---

## 3. Typography: Editorial Authority

We use a dual-font strategy to balance character with readability.

*   **Display & Headlines (Manrope):** Chosen for its modern, geometric structure. Large `display-lg` (3.5rem) scales should be used with tight letter-spacing (-0.02em) to create a "punchy" marketing impact.
*   **Body & Labels (Inter):** A workhorse for clarity. `body-lg` (1rem) is the standard for long-form sales copy to ensure maximum legibility.

**Hierarchy as Identity:**
- **The Hook:** Use `display-md` for value propositions.
- **The Narrative:** Use `body-lg` for descriptive text.
- **The Action:** Use `label-md` in all-caps for utility elements like breadcrumbs or category chips.

---

## 4. Elevation & Depth: Tonal Layering

Traditional shadows are a fallback; **Tonal Layering** is the primary method for establishing hierarchy.

- **The Layering Principle:** Place a `surface-container-lowest` card on top of a `surface-container-low` section. This creates a natural, "organic" lift that feels high-end and intentional.
- **Ambient Shadows:** When a shadow is required (e.g., a floating CTA button), it must be ultra-diffused. 
    - *Spec:* `0px 10px 40px rgba(28, 28, 22, 0.06)` (using a tinted version of `on-surface`).
- **The Ghost Border:** If a boundary is required for accessibility, use a "Ghost Border": `outline-variant` (#c2c7ca) at **15% opacity**. Never use 100% opaque borders.
- **Glassmorphism:** Use `surface-container-lowest` at 70% opacity with `backdrop-filter: blur(12px)` for headers to keep the content feeling integrated as the user scrolls.

---

## 5. Components: Precision Marketing

### Buttons
- **Primary (Conversion):** Background: Gradient (`primary` to `primary-container`). Text: `on-primary` (#ffffff). Border-radius: `md` (0.375rem). High-contrast and punchy.
- **Secondary (Inquiry):** Background: `secondary-container` (#e4e2e2). Text: `on-secondary-fixed` (#1b1c1c).
- **Tertiary (Navigational):** No background. Text: `primary`. Use for "Learn More" links with a trailing arrow icon.

### Cards & Product Showcases
**Forbid the use of divider lines.** To separate product features (like Pulse TV vs Pulse Cast):
1.  Increase vertical padding using the `16` (4rem) spacing token.
2.  Shift the background color of the card container to `surface-container-high` (#ece8de).
3.  Use asymmetrical layouts: Image on the left for item one, image on the right for item two.

### Input Fields
- **Container:** `surface-container-highest` (#e6e2d8). 
- **Focus State:** `primary` (#01151c) Ghost Border (2px at 40% opacity).
- **Labels:** `label-md` in `on-surface-variant` (#42484a).

### Chips (Category Tags)
- Use `tertiary-fixed` (#d1e7df) with `on-tertiary-fixed` (#0b1f1a) for "New" or "Active" statuses.
- Use `secondary-fixed` (#e4e2e2) for neutral categorization.
- Radius: `full` (9999px).

---

## 6. Do's and Don'ts

### Do
- **Do** use `24` (6rem) spacing for major section breaks to allow the "modern SaaS" aesthetic to breathe.
- **Do** layer `surface-container-lowest` cards on `surface-container` backgrounds to highlight key "Showcase" items.
- **Do** use `display-lg` typography for hero statements to immediately capture professional interest.
- **Do** utilize the tree logo in a subtle, watermark fashion or as a high-quality "stamp" in the footer.

### Don't
- **Don't** use 1px solid black or grey borders to separate sections.
- **Don't** use standard Arial for headlines; restrict Arial-adjacent looks to technical body copy, favoring Manrope for all marketing-heavy headings.
- **Don't** use harsh drop shadows with 20%+ opacity.
- **Don't** crowd the layout. If a section feels "busy," increase the spacing token by two increments.