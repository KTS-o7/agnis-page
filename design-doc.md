# Design System: Fiery Coastal Street Kitchen

## 1. Overview & Creative North Star
**The Creative North Star: "The Embers of the Coast"**

This design system rejects the sterile, "safe" layouts of corporate food apps. It is a high-octane, editorial experience that mirrors the heat of a street-side grill and the depth of a midnight ocean. We achieve this through **Kinetic Asymmetry** and **Tonal Depth**. Instead of a rigid grid, we use overlapping elements, intentional whitespace, and "spicy" typography to create a raw, Instagram-ready aesthetic that feels alive.

The goal is to make the user feel the heat. We avoid fine-dining elegance in favor of "Premium Grit"—where high-end design meets the energy of the street.

---

### 2. Colors & Surface Philosophy
The palette is built on a foundation of `surface` (#0e0e0e) to let the food photography—the true hero—pop with maximum intensity.

*   **Primary Foundation:** `primary` (#ff8f70) and `primary_fixed_dim` (#ff5d2e) represent the core heat. Use these for high-action CTAs.
*   **The Glow:** `secondary` (#feb300) and `secondary_dim` (#eda600) provide the "sunshine" and golden-fried crispness.
*   **The Burn:** `tertiary` (#ff7168) is reserved for spice indicators and urgent alerts.

#### The "No-Line" Rule
**Explicit Instruction:** Do not use 1px solid borders to define sections. We define boundaries through **background shifts**. A card using `surface_container_highest` (#262626) should sit on a `surface` (#0e0e0e) background. The contrast is the container.

#### The "Glass & Gradient" Rule
To add "soul," avoid flat blocks of color. 
*   **Signature Gradients:** For Hero CTAs, use a linear gradient from `primary` (#ff8f70) to `primary_container` (#ff7852) at a 135° angle.
*   **Glassmorphism:** For floating navigation or spice-level overlays, use `surface_container` at 70% opacity with a `backdrop-filter: blur(12px)`. This creates an "urban steam" effect.

---

### 3. Typography
We use a high-contrast pairing to balance raw energy with professional legibility.

*   **Display & Headlines (Epilogue):** This is our "Spicy" voice. It is bold, modern, and unapologetic. Use `display-lg` (3.5rem) for hero headlines, often with tight tracking (-0.02em) to create a compact, heavy-hitting feel.
*   **Body & Titles (Be Vietnam Pro):** This is our "Clean" voice. It provides the necessary breathability. Use `body-lg` (1rem) for descriptions to ensure the menu remains readable under neon lights or on a moving bus.
*   **Hierarchy Note:** Use `on_primary_fixed` (#000000) text over `primary` backgrounds for maximum punch. Never use grey text on colored buttons.

---

### 4. Elevation & Depth
We abandon the "shadow-heavy" look of 2010s apps. Depth is achieved through **Tonal Layering**.

*   **The Layering Principle:** 
    *   **Level 0 (Base):** `surface` (#0e0e0e)
    *   **Level 1 (Sections):** `surface_container_low` (#131313)
    *   **Level 2 (Cards):** `surface_container_highest` (#262626)
*   **Ambient Shadows:** If an element must float (like a "Cart" FAB), use a shadow tinted with `primary_dim` (#ff734c) at 5% opacity. This mimics the warm glow of a heat lamp rather than a cold grey shadow.
*   **The Ghost Border:** If accessibility requires a stroke, use `outline_variant` (#484847) at **15% opacity**. It should be felt, not seen.

---

### 5. Components

#### High-Impact Food Cards
*   **Structure:** No borders. Use a `surface_container_highest` (#262626) base.
*   **Visuals:** The image should break the container. Use a slight `scale(1.05)` on hover.
*   **Spacing:** Use `spacing.6` (1.5rem) for internal padding to give the "street kitchen" vibe room to breathe.

#### Bold Buttons
*   **Primary:** Background `primary`, text `on_primary_fixed` (#000000). Use `rounded.md` (0.375rem)—avoid full-round pills to maintain a "raw" edge.
*   **Secondary:** Background `surface_container_highest`, 15% `outline` stroke, text `on_surface`.
*   **Interaction:** On tap/hover, transition to `primary_fixed_dim`.

#### Spice Level Indicators (🌶️)
*   **Styling:** Use `tertiary` (#ff7168) for active spice icons and `outline_variant` at 30% for inactive.
*   **Layout:** Place these in the top-right of food cards using a glassmorphic background (`surface_bright` at 60% opacity).

#### Sticky Mobile Navigation
*   **Design:** A floating bar at the bottom. Use `surface_container` (#1a1a1a) with a `backdrop-blur` of 20px. 
*   **Layout:** Asymmetric icons. The "Order Now" center action should be larger, using the `secondary` (#feb300) token to act as a visual anchor.

#### Input Fields
*   **Style:** Underline-only or subtle `surface_variant` (#262626) blocks. No 4-sided boxes.
*   **Focus State:** The underline transitions from `outline` to `primary` (#ff8f70) with a 2px thickness.

---

### 6. Do’s and Don'ts

#### Do
*   **DO** use photography with hard shadows and high saturation to match the "Fiery" theme.
*   **DO** use `spacing.10` or `spacing.12` between major sections to prevent the dark UI from feeling "cramped."
*   **DO** overlap text onto images slightly to create an editorial, magazine-style layout.

#### Don't
*   **DON'T** use pure white (#ffffff) for large blocks of body text; use `on_surface_variant` (#adaaaa) to reduce eye strain on dark backgrounds.
*   **DON'T** use standard blue for links. Use `secondary` (#feb300).
*   **DON'T** use `rounded.full` for buttons; it feels too "tech-corporate." Stick to `rounded.md` or `rounded.lg`.
*   **DON'T** use dividers. If you feel the need for a line, increase the `spacing` token instead. Vertical whitespace is your divider.
