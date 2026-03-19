# Agni's Restaurant Page Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a static, SEO-optimised restaurant landing page for "Agni's Fish Chicken Fries" (Mangalore street kitchen) with a Homepage and Menu page, deployed on Vercel.

**Architecture:** Astro 6 static site (`output: 'static'`), zero client JS, all content from typed JSON data files. The `@astrojs/vercel/static` adapter handles deployment. Tailwind v4 provides styling via the `@tailwindcss/vite` plugin — no PostCSS config needed.

**Tech Stack:** Astro 6, Tailwind CSS v4, Bun 1.3.6, `@astrojs/vercel` adapter, `@astrojs/sitemap` for SEO, Google Fonts (Epilogue + Be Vietnam Pro) via `@fontsource` packages for self-hosting.

---

## Reference: Design System Tokens

These are used throughout — memorise them before touching any component.

| Token | Value | Usage |
|---|---|---|
| `surface` | `#0e0e0e` | Page background |
| `surface-container-low` | `#131313` | Section backgrounds |
| `surface-container` | `#1a1a1a` | Nav, bottom bar |
| `surface-container-highest` | `#262626` | Cards |
| `primary` | `#ff8f70` | CTAs, active links |
| `primary-fixed-dim` | `#ff5d2e` | CTA hover |
| `primary-container` | `#ff7852` | Gradient end |
| `secondary` | `#feb300` | Gold accent, order button |
| `tertiary` | `#ff7168` | Spice indicators, alerts |
| `on-surface` | `#ffffff` | Body text |
| `on-surface-variant` | `#adaaaa` | Muted text |
| `on-primary-fixed` | `#000000` | Text on primary bg |
| `outline-variant` | `#484847` | Ghost borders |
| `font-headline` | Epilogue | Display, H1-H3 |
| `font-body` | Be Vietnam Pro | Body, labels |

**Design rules:**
- No 1px borders — use background contrast instead
- No dividers — use vertical spacing instead
- No `rounded-full` on buttons — use `rounded-md` or `rounded-lg`
- No pure white for body text — use `on-surface-variant`
- Links use `secondary` colour, never blue

---

## Task 1: Scaffold Astro project with Bun

**Files:**
- Create: `package.json`, `astro.config.ts`, `tsconfig.json`, `src/` tree

**Step 1: Initialise Astro with Bun**

```bash
bun create astro@latest . --template minimal --typescript strict --no-git --install
```

When prompted: TypeScript = strict, no git (already initialised).

**Step 2: Install Tailwind v4 Astro integration**

```bash
bun astro add tailwind
```

When prompted, accept all defaults. This installs `@astrojs/tailwind` and `tailwindcss`.

> **Note:** Tailwind v4 with Astro uses `@tailwindcss/vite` under the hood — the integration handles this automatically. No `tailwind.config.js` or `postcss.config.js` needed.

**Step 3: Install Vercel adapter**

```bash
bun astro add vercel
```

Accept defaults. This adds `@astrojs/vercel` and sets `output: 'static'` in `astro.config.ts`.

**Step 4: Install sitemap integration**

```bash
bun astro add sitemap
```

**Step 5: Install self-hosted fonts**

```bash
bun add @fontsource/epilogue @fontsource/be-vietnam-pro
```

**Step 6: Verify `astro.config.ts` looks like this**

```ts
import { defineConfig } from 'astro/config';
import tailwind from '@astrojs/tailwind';
import vercel from '@astrojs/vercel/static';
import sitemap from '@astrojs/sitemap';

export default defineConfig({
  site: 'https://agnis-page.vercel.app',
  output: 'static',
  adapter: vercel(),
  integrations: [tailwind(), sitemap()],
});
```

**Step 7: Commit**

```bash
git add -A
git commit -m "chore: scaffold Astro 6 + Tailwind v4 + Vercel adapter"
```

---

## Task 2: Tailwind v4 design tokens

**Files:**
- Create: `src/styles/global.css`
- Modify: `src/styles/global.css` (add all tokens)

**Step 1: Create global CSS with Tailwind v4 import and design tokens**

Tailwind v4 uses CSS-native `@theme` — no JS config file needed.

```css
/* src/styles/global.css */
@import "tailwindcss";

@theme {
  /* Surfaces */
  --color-surface: #0e0e0e;
  --color-surface-dim: #0e0e0e;
  --color-surface-bright: #2c2c2c;
  --color-surface-container-lowest: #000000;
  --color-surface-container-low: #131313;
  --color-surface-container: #1a1a1a;
  --color-surface-container-high: #20201f;
  --color-surface-container-highest: #262626;
  --color-surface-variant: #262626;

  /* Primary */
  --color-primary: #ff8f70;
  --color-primary-dim: #ff734c;
  --color-primary-fixed: #ff7852;
  --color-primary-fixed-dim: #ff5d2e;
  --color-primary-container: #ff7852;
  --color-on-primary: #5c1300;
  --color-on-primary-fixed: #000000;
  --color-on-primary-fixed-variant: #581200;
  --color-on-primary-container: #480d00;
  --color-inverse-primary: #b22e00;

  /* Secondary */
  --color-secondary: #feb300;
  --color-secondary-dim: #eda600;
  --color-secondary-fixed: #ffc96f;
  --color-secondary-fixed-dim: #ffb623;
  --color-secondary-container: #7e5700;
  --color-on-secondary: #523700;
  --color-on-secondary-fixed: #483000;
  --color-on-secondary-fixed-variant: #6c4a00;
  --color-on-secondary-container: #fff6ee;

  /* Tertiary */
  --color-tertiary: #ff7168;
  --color-tertiary-dim: #ff7168;
  --color-tertiary-fixed: #ff9289;
  --color-tertiary-fixed-dim: #ff7b71;
  --color-tertiary-container: #fe4e49;
  --color-on-tertiary: #4a0004;
  --color-on-tertiary-fixed: #3a0002;
  --color-on-tertiary-fixed-variant: #7a000b;
  --color-on-tertiary-container: #1f0001;

  /* Error */
  --color-error: #ff716c;
  --color-error-dim: #d7383b;
  --color-error-container: #9f0519;
  --color-on-error: #490006;
  --color-on-error-container: #ffa8a3;

  /* Neutrals */
  --color-background: #0e0e0e;
  --color-on-background: #ffffff;
  --color-on-surface: #ffffff;
  --color-on-surface-variant: #adaaaa;
  --color-outline: #767575;
  --color-outline-variant: #484847;
  --color-inverse-surface: #fcf9f8;
  --color-inverse-on-surface: #565555;
  --color-surface-tint: #ff8f70;

  /* Typography */
  --font-headline: "Epilogue", sans-serif;
  --font-body: "Be Vietnam Pro", sans-serif;
  --font-label: "Be Vietnam Pro", sans-serif;

  /* Border radius */
  --radius-DEFAULT: 0.125rem;
  --radius-lg: 0.25rem;
  --radius-xl: 0.5rem;
  --radius-full: 0.75rem;
}

/* Reusable utility classes */
.kinetic-gradient {
  background: linear-gradient(135deg, #ff8f70 0%, #ff7852 100%);
}

.glass-panel {
  background: rgba(26, 26, 26, 0.7);
  backdrop-filter: blur(12px);
}
```

**Step 2: Commit**

```bash
git add src/styles/global.css
git commit -m "feat: add Tailwind v4 design system tokens"
```

---

## Task 3: Content data files (menu JSON)

**Files:**
- Create: `src/data/menu.json`
- Create: `src/data/site.json`

**Step 1: Create `src/data/site.json`**

```json
{
  "name": "Agni's",
  "tagline": "Fish Chicken Fries",
  "description": "Fresh. Fiery. Made to Order. Burgers, Seafood & Fries — Kudla Style.",
  "address": "Near Besant College, MG Road, Mangaluru, Karnataka 575003",
  "phone": "+91 98765 43210",
  "whatsapp": "919876543210",
  "hours": "Monday — Sunday, 12:30 PM – 10:30 PM",
  "social": {
    "instagram": "#",
    "facebook": "#"
  }
}
```

**Step 2: Create `src/data/menu.json`**

```json
{
  "categories": [
    {
      "id": "burgers",
      "label": "Burgers",
      "items": [
        {
          "id": "classic-burger",
          "name": "Classic Burger",
          "description": "Juicy hand-pressed patty, secret house sauce, and charred buns.",
          "price": 189,
          "currency": "₹",
          "spiceLevel": 1,
          "tags": ["best-seller"],
          "image": "/images/classic-burger.jpg",
          "imageAlt": "Classic beef burger with lettuce and tomato"
        },
        {
          "id": "inferno-burger",
          "name": "The Inferno Burger",
          "description": "Double beef patty, ghost pepper jack, charred jalapeños, and our signature Agni lava sauce.",
          "price": 249,
          "currency": "₹",
          "spiceLevel": 3,
          "tags": [],
          "image": "/images/inferno-burger.jpg",
          "imageAlt": "A thick juicy beef burger with melting cheese and spicy sauce"
        },
        {
          "id": "citrus-fire-chicken",
          "name": "Citrus Fire Chicken",
          "description": "Buttermilk fried chicken, lemon-zest slaw, and a mild peri-peri glaze on brioche.",
          "price": 219,
          "currency": "₹",
          "spiceLevel": 1,
          "tags": [],
          "image": "/images/citrus-fire-chicken.jpg",
          "imageAlt": "Golden fried chicken breast burger with crisp lettuce"
        }
      ]
    },
    {
      "id": "seafood",
      "label": "Seafood",
      "items": [
        {
          "id": "spicy-fish-fry",
          "name": "Spicy Fish Fry",
          "description": "Fresh catch marinated in Agni's special coastal masala blend.",
          "price": 249,
          "currency": "₹",
          "spiceLevel": 2,
          "tags": ["best-seller"],
          "image": "/images/spicy-fish-fry.jpg",
          "imageAlt": "Spicy coastal fried fish fillet with local spices"
        },
        {
          "id": "agnis-signature-fish",
          "name": "Agni's Signature Fish",
          "description": "Secret malvani rub, charcoal grilled to perfection.",
          "price": 340,
          "currency": "₹",
          "spiceLevel": 2,
          "tags": ["best-seller"],
          "image": "/images/signature-fish.jpg",
          "imageAlt": "Grilled fish fillet with herbs"
        },
        {
          "id": "dynamite-prawns",
          "name": "Dynamite Prawns",
          "description": "Jumbo prawns battered and tossed in a creamy, sriracha-infused dynamite sauce.",
          "price": 320,
          "currency": "₹",
          "spiceLevel": 2,
          "tags": [],
          "image": "/images/dynamite-prawns.jpg",
          "imageAlt": "Crispy breaded shrimp with a spicy dipping sauce"
        }
      ]
    },
    {
      "id": "chicken",
      "label": "Chicken",
      "items": [
        {
          "id": "chicken-tenders",
          "name": "Chicken Tenders",
          "description": "Double-breaded for extra crunch. Served with hot garlic mayo.",
          "price": 169,
          "currency": "₹",
          "spiceLevel": 1,
          "tags": [],
          "image": "/images/chicken-tenders.jpg",
          "imageAlt": "Golden crispy chicken tenders with dip"
        },
        {
          "id": "dynamite-strips",
          "name": "Dynamite Strips",
          "description": "Crispy breast strips with ghost pepper mayo.",
          "price": 220,
          "currency": "₹",
          "spiceLevel": 3,
          "tags": ["best-seller"],
          "image": "/images/dynamite-strips.jpg",
          "imageAlt": "Crispy fried chicken strips"
        }
      ]
    },
    {
      "id": "fries",
      "label": "Fries",
      "items": [
        {
          "id": "peri-peri-fries",
          "name": "Peri Peri Fries",
          "description": "Hand-cut potato fries dusted with African Bird's Eye chili.",
          "price": 129,
          "currency": "₹",
          "spiceLevel": 2,
          "tags": [],
          "image": "/images/peri-peri-fries.jpg",
          "imageAlt": "Fries tossed in red peri peri spice powder"
        },
        {
          "id": "masala-dust-fries",
          "name": "Masala Dust Fries",
          "description": "Twice-fried with our secret blend of coastal spices.",
          "price": 119,
          "currency": "₹",
          "spiceLevel": 1,
          "tags": [],
          "image": "/images/masala-dust-fries.jpg",
          "imageAlt": "Golden fries sprinkled with red masala spice"
        },
        {
          "id": "volcano-loaded",
          "name": "Volcano Loaded",
          "description": "Smothered in melted cheddar and Agni hot sauce.",
          "price": 179,
          "currency": "₹",
          "spiceLevel": 3,
          "tags": [],
          "image": "/images/volcano-loaded.jpg",
          "imageAlt": "Loaded fries with melted cheese and spicy sauce"
        }
      ]
    },
    {
      "id": "combos",
      "label": "Combos",
      "items": [
        {
          "id": "coastal-ember-feast",
          "name": "The Coastal Ember Feast",
          "description": "Two pieces of crispy fire-grilled fish, four signature spicy wings, hand-cut cajun fries, and our house-made lemon-garlic aioli.",
          "price": 549,
          "currency": "₹",
          "spiceLevel": 3,
          "tags": ["chef-special"],
          "image": "/images/coastal-ember-feast.jpg",
          "imageAlt": "Close up of a gourmet fried chicken and fish combo platter"
        }
      ]
    }
  ],
  "topSellers": ["classic-burger", "spicy-fish-fry", "agnis-signature-fish", "peri-peri-fries"]
}
```

**Step 3: Commit**

```bash
git add src/data/
git commit -m "feat: add site and menu content data files"
```

---

## Task 4: Base layout component

**Files:**
- Create: `src/layouts/BaseLayout.astro`

**Step 1: Create the layout**

This layout handles: font imports, global CSS, `<head>` SEO meta, glassmorphic top nav, mobile bottom nav bar.

```astro
---
// src/layouts/BaseLayout.astro
import '../styles/global.css';
import '@fontsource/epilogue/700.css';
import '@fontsource/epilogue/800.css';
import '@fontsource/epilogue/900.css';
import '@fontsource/be-vietnam-pro/300.css';
import '@fontsource/be-vietnam-pro/400.css';
import '@fontsource/be-vietnam-pro/500.css';
import '@fontsource/be-vietnam-pro/700.css';
import site from '../data/site.json';

interface Props {
  title: string;
  description?: string;
  ogImage?: string;
  activePage?: 'home' | 'menu' | 'location' | 'contact';
}

const {
  title,
  description = site.description,
  ogImage = '/og-image.jpg',
  activePage = 'home',
} = Astro.props;

const canonicalURL = new URL(Astro.url.pathname, Astro.site);

const navLinks = [
  { label: 'Home', href: '/', key: 'home' },
  { label: 'Menu', href: '/menu', key: 'menu' },
  { label: 'Location', href: '/#location', key: 'location' },
  { label: 'Contact', href: '/#contact', key: 'contact' },
];
---

<!doctype html>
<html lang="en" class="dark">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <link rel="canonical" href={canonicalURL} />
    <title>{title}</title>

    <!-- Open Graph -->
    <meta property="og:title" content={title} />
    <meta property="og:description" content={description} />
    <meta property="og:image" content={ogImage} />
    <meta property="og:url" content={canonicalURL} />
    <meta property="og:type" content="website" />
    <meta property="og:locale" content="en_IN" />

    <!-- Twitter Card -->
    <meta name="twitter:card" content="summary_large_image" />
    <meta name="twitter:title" content={title} />
    <meta name="twitter:description" content={description} />
    <meta name="twitter:image" content={ogImage} />

    <!-- Schema.org structured data -->
    <script type="application/ld+json" set:html={JSON.stringify({
      "@context": "https://schema.org",
      "@type": "Restaurant",
      "name": site.name,
      "description": site.description,
      "address": {
        "@type": "PostalAddress",
        "streetAddress": site.address,
      },
      "telephone": site.phone,
      "openingHours": "Mo-Su 12:30-22:30",
      "servesCuisine": ["Seafood", "Chicken", "Burgers"],
      "priceRange": "₹₹",
    })} />
  </head>

  <body class="bg-surface text-on-surface font-body selection:bg-primary selection:text-on-primary-fixed">

    <!-- Top Navigation -->
    <header class="fixed top-0 w-full flex justify-between items-center px-6 py-4 z-50 bg-surface/70 backdrop-blur-xl">
      <a href="/" class="text-2xl font-black text-primary uppercase tracking-tighter font-headline">
        {site.name}
      </a>
      <nav class="hidden md:flex items-center space-x-8 font-headline font-black tracking-tight text-on-surface" aria-label="Main navigation">
        {navLinks.map(link => (
          <a
            href={link.href}
            class={`transition-all duration-300 ${
              activePage === link.key
                ? 'text-secondary font-bold border-b-2 border-secondary pb-1'
                : 'text-on-surface-variant hover:text-secondary'
            }`}
          >
            {link.label}
          </a>
        ))}
      </nav>
      <button class="md:hidden text-primary p-2" aria-label="Open menu" aria-expanded="false" id="mobile-menu-btn">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <line x1="3" y1="6" x2="21" y2="6"/>
          <line x1="3" y1="12" x2="21" y2="12"/>
          <line x1="3" y1="18" x2="21" y2="18"/>
        </svg>
      </button>
    </header>

    <!-- Page content -->
    <main>
      <slot />
    </main>

    <!-- Footer -->
    <footer class="w-full flex flex-col items-center text-center space-y-8 bg-surface py-12 px-6 border-t border-outline-variant/15">
      <div class="text-xl font-black text-primary uppercase tracking-tighter font-headline">{site.name}</div>
      <nav class="flex flex-wrap justify-center gap-8 font-body text-sm text-on-surface-variant" aria-label="Footer navigation">
        <a class="hover:text-primary transition-colors" href="/privacy">Privacy Policy</a>
        <a class="hover:text-primary transition-colors" href="/terms">Terms of Service</a>
        <a class="hover:text-primary transition-colors" href="/#location">Location</a>
        <a class="hover:text-primary transition-colors" href="/#contact">Contact</a>
      </nav>
      <p class="font-body text-sm text-on-surface-variant">© {new Date().getFullYear()} {site.name} Fish Chicken Fries. Embers of the Coast.</p>
    </footer>

    <!-- Mobile Bottom Navigation -->
    <nav
      class="md:hidden fixed bottom-0 left-0 w-full z-50 flex justify-around items-center px-4 pb-6 pt-2 bg-surface-container/80 backdrop-blur-2xl rounded-t-2xl shadow-[0_-4px_20px_rgba(255,115,76,0.05)]"
      aria-label="Mobile navigation"
    >
      <a href={`https://wa.me/${site.whatsapp}`} class="flex flex-col items-center justify-center text-on-surface-variant px-4 py-2 hover:text-primary transition-colors active:scale-95" target="_blank" rel="noopener noreferrer">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
          <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347z"/>
          <path d="M12 0C5.373 0 0 5.373 0 12c0 2.124.554 4.122 1.526 5.858L0 24l6.266-1.502A11.94 11.94 0 0 0 12 24c6.627 0 12-5.373 12-12S18.627 0 12 0zm0 21.818a9.818 9.818 0 0 1-5.012-1.374l-.36-.214-3.72.892.926-3.618-.235-.372A9.818 9.818 0 1 1 12 21.818z"/>
        </svg>
        <span class="font-body text-[10px] font-bold uppercase tracking-widest mt-1">WhatsApp</span>
      </a>
      <a href="/menu" class="flex flex-col items-center justify-center bg-secondary text-on-primary-fixed rounded-xl px-6 py-2 scale-110 active:scale-95 transition-all shadow-lg shadow-secondary/20">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
          <path d="M7 18c-1.1 0-1.99.9-1.99 2S5.9 22 7 22s2-.9 2-2-.9-2-2-2zm10 0c-1.1 0-1.99.9-1.99 2S15.9 22 17 22s2-.9 2-2-.9-2-2-2zm-9.83-3.25l.03-.12.9-1.63H15.55c.75 0 1.41-.41 1.75-1.03l3.86-7.01L19.42 4h-.01l-1.1 2-2.76 5H8.53l-.13-.27L6.16 6l-.95-2-.94-2H1v2h2l3.6 7.59-1.35 2.45c-.16.28-.25.61-.25.96C5 15.1 5.9 16 7 16h12v-2H7.42c-.13 0-.25-.11-.25-.25z"/>
        </svg>
        <span class="font-body text-[10px] font-bold uppercase tracking-widest mt-1">Menu</span>
      </a>
      <a href={`tel:${site.phone}`} class="flex flex-col items-center justify-center text-on-surface-variant px-4 py-2 hover:text-primary transition-colors active:scale-95">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
          <path d="M6.62 10.79c1.44 2.83 3.76 5.14 6.59 6.59l2.2-2.2c.27-.27.67-.36 1.02-.24 1.12.37 2.33.57 3.57.57.55 0 1 .45 1 1V20c0 .55-.45 1-1 1-9.39 0-17-7.61-17-17 0-.55.45-1 1-1h3.5c.55 0 1 .45 1 1 0 1.25.2 2.45.57 3.57.11.35.03.74-.25 1.02l-2.2 2.2z"/>
        </svg>
        <span class="font-body text-[10px] font-bold uppercase tracking-widest mt-1">Call</span>
      </a>
    </nav>

  </body>
</html>
```

**Step 2: Commit**

```bash
git add src/layouts/BaseLayout.astro
git commit -m "feat: add BaseLayout with nav, footer, mobile bottom bar"
```

---

## Task 5: Reusable components

**Files:**
- Create: `src/components/FoodCard.astro`
- Create: `src/components/SpiceIndicator.astro`
- Create: `src/components/SectionLabel.astro`

**Step 1: Create `SpiceIndicator.astro`**

```astro
---
// src/components/SpiceIndicator.astro
interface Props {
  level: 1 | 2 | 3;
  max?: number;
}
const { level, max = 3 } = Astro.props;
---
<div class="flex gap-0.5" aria-label={`Spice level ${level} of ${max}`} role="img">
  {Array.from({ length: max }).map((_, i) => (
    <svg
      xmlns="http://www.w3.org/2000/svg"
      width="12"
      height="12"
      viewBox="0 0 24 24"
      fill="currentColor"
      class={i < level ? 'text-tertiary' : 'text-outline-variant/30'}
      aria-hidden="true"
    >
      <path d="M13.5.67s.74 2.65.74 4.8c0 2.06-1.35 3.73-3.41 3.73-2.07 0-3.63-1.67-3.63-3.73l.03-.36C5.21 7.51 4 10.62 4 14c0 4.42 3.58 8 8 8s8-3.58 8-8C20 8.61 17.41 3.8 13.5.67zM11.71 19c-1.78 0-3.22-1.4-3.22-3.14 0-1.62 1.05-2.76 2.81-3.12 1.77-.36 3.6-1.21 4.62-2.58.39 1.29.59 2.65.59 4.04 0 2.65-2.15 4.8-4.8 4.8z"/>
    </svg>
  ))}
</div>
```

**Step 2: Create `SectionLabel.astro`**

```astro
---
// src/components/SectionLabel.astro
interface Props {
  text: string;
}
const { text } = Astro.props;
---
<span class="text-secondary font-bold tracking-[0.2em] uppercase text-sm font-body">
  {text}
</span>
```

**Step 3: Create `FoodCard.astro`**

```astro
---
// src/components/FoodCard.astro
import SpiceIndicator from './SpiceIndicator.astro';

interface Props {
  name: string;
  description: string;
  price: number;
  currency: string;
  spiceLevel: 1 | 2 | 3;
  tags: string[];
  image: string;
  imageAlt: string;
  layout?: 'grid' | 'list';
}

const {
  name,
  description,
  price,
  currency,
  spiceLevel,
  tags,
  image,
  imageAlt,
  layout = 'grid',
} = Astro.props;

const isBestSeller = tags.includes('best-seller');
const isChefSpecial = tags.includes('chef-special');
---

{layout === 'grid' ? (
  <article class="group relative bg-surface-container-highest rounded-xl overflow-hidden p-6 transition-all hover:-translate-y-2">
    {(isBestSeller || isChefSpecial) && (
      <div class="absolute top-4 right-4 z-20 glass-panel px-3 py-1 rounded-full flex items-center gap-1">
        <span class="text-tertiary text-sm" aria-hidden="true">🔥</span>
        <span class="text-[10px] font-bold uppercase tracking-tighter font-body">
          {isBestSeller ? 'Best Seller' : "Chef's Special"}
        </span>
      </div>
    )}
    <div class="aspect-square mb-6 overflow-visible">
      <img
        src={image}
        alt={imageAlt}
        class="w-full h-full object-contain group-hover:scale-110 transition-transform duration-500 drop-shadow-2xl"
        loading="lazy"
        decoding="async"
        width="400"
        height="400"
      />
    </div>
    <h3 class="font-headline text-2xl font-bold mb-2">{name}</h3>
    <p class="text-on-surface-variant text-sm mb-4">{description}</p>
    <SpiceIndicator level={spiceLevel} />
    <div class="flex items-center justify-between mt-4">
      <span class="text-primary font-bold text-xl font-body">{currency}{price}</span>
    </div>
  </article>
) : (
  <article class="group bg-surface-container-highest p-4 rounded-xl flex gap-4 items-center transition-all hover:bg-surface-container-high active:scale-[0.98]">
    <div class="relative h-24 w-24 flex-shrink-0 rounded-lg overflow-hidden">
      <img
        src={image}
        alt={imageAlt}
        class="h-full w-full object-cover group-hover:scale-110 transition-transform"
        loading="lazy"
        decoding="async"
        width="96"
        height="96"
      />
      {isBestSeller && (
        <div class="absolute top-1 left-1 bg-tertiary text-[10px] font-black px-1.5 py-0.5 rounded text-on-tertiary uppercase font-body">🔥 Best</div>
      )}
    </div>
    <div class="flex-1 min-w-0">
      <div class="flex justify-between items-start">
        <h3 class="font-bold text-lg leading-tight">{name}</h3>
        <span class="text-primary font-bold ml-2 whitespace-nowrap font-body">{currency}{price}</span>
      </div>
      <p class="text-on-surface-variant text-sm mt-1 line-clamp-2">{description}</p>
      <div class="mt-2">
        <SpiceIndicator level={spiceLevel} />
      </div>
    </div>
  </article>
)}
```

**Step 4: Commit**

```bash
git add src/components/
git commit -m "feat: add FoodCard, SpiceIndicator, SectionLabel components"
```

---

## Task 6: Homepage (`/`)

**Files:**
- Create: `src/pages/index.astro`

**Step 1: Create the homepage**

The homepage has these sections (in order):
1. Hero — full-screen with background image, headline, WhatsApp CTA
2. Top Sellers — 4-col bento grid of best-seller cards
3. Brand Story — 2-col layout with image + copy
4. Social Proof — masonry-style photo/quote grid
5. Location & Map — address, hours, map image

```astro
---
// src/pages/index.astro
import BaseLayout from '../layouts/BaseLayout.astro';
import FoodCard from '../components/FoodCard.astro';
import SectionLabel from '../components/SectionLabel.astro';
import menuData from '../data/menu.json';
import siteData from '../data/site.json';

// Resolve top seller items from all categories
const allItems = menuData.categories.flatMap(cat => cat.items);
const topSellers = menuData.topSellers
  .map(id => allItems.find(item => item.id === id))
  .filter(Boolean);
---

<BaseLayout
  title={`${siteData.name} – Fish Chicken Fries | Fresh. Fiery. Made to Order.`}
  activePage="home"
>
  <!-- Hero -->
  <section class="relative min-h-screen flex items-center pt-20 overflow-hidden">
    <div class="absolute inset-0 z-0">
      <img
        src="/images/hero-bg.jpg"
        alt=""
        role="presentation"
        class="w-full h-full object-cover brightness-[0.4] scale-105"
        fetchpriority="high"
        decoding="async"
        width="1920"
        height="1080"
      />
      <div class="absolute inset-0 bg-gradient-to-t from-surface via-transparent to-surface/40"></div>
    </div>
    <div class="relative z-10 container mx-auto px-6">
      <div class="max-w-4xl">
        <h1 class="font-headline text-6xl md:text-8xl font-black tracking-tight leading-[0.9] mb-6">
          Fresh. <span class="text-primary italic">Fiery.</span><br />Made to Order.
        </h1>
        <p class="font-body text-xl md:text-2xl text-on-surface-variant mb-10 max-w-xl">
          Burgers • Seafood • Fries —
          <span class="text-secondary font-bold tracking-widest uppercase">Kudla Style</span>
        </p>
        <div class="flex flex-wrap gap-4">
          <a
            href={`https://wa.me/${siteData.whatsapp}`}
            target="_blank"
            rel="noopener noreferrer"
            class="kinetic-gradient text-on-primary-fixed px-8 py-4 rounded-md font-bold text-lg flex items-center gap-2 hover:brightness-110 active:scale-95 transition-all"
          >
            Order on WhatsApp
          </a>
          <a
            href="/#location"
            class="bg-surface-container-highest border border-outline-variant/15 text-on-surface px-8 py-4 rounded-md font-bold text-lg hover:bg-surface-container-high transition-all"
          >
            Find Us
          </a>
        </div>
      </div>
    </div>
  </section>

  <!-- Top Sellers -->
  <section class="py-24 px-6 container mx-auto">
    <div class="flex flex-col md:flex-row md:items-end justify-between mb-16 gap-4">
      <div>
        <SectionLabel text="Most Wanted" />
        <h2 class="font-headline text-5xl font-extrabold mt-2">Top Sellers</h2>
      </div>
      <p class="text-on-surface-variant max-w-xs border-l-2 border-primary pl-4">
        The heat that defines Mangalore street flavors. Hand-picked favorites.
      </p>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
      {topSellers.map(item => (
        <FoodCard
          name={item.name}
          description={item.description}
          price={item.price}
          currency={item.currency}
          spiceLevel={item.spiceLevel as 1|2|3}
          tags={item.tags}
          image={item.image}
          imageAlt={item.imageAlt}
          layout="grid"
        />
      ))}
    </div>
  </section>

  <!-- Brand Story -->
  <section class="py-24 bg-surface-container-low overflow-hidden">
    <div class="container mx-auto px-6">
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-16 items-center">
        <div class="relative">
          <div class="absolute -top-10 -left-10 w-64 h-64 bg-primary/10 rounded-full blur-[100px]" aria-hidden="true"></div>
          <img
            src="/images/brand-story.jpg"
            alt="Chef preparing food on a hot grill with flames"
            class="rounded-2xl relative z-10 w-full h-[600px] object-cover"
            loading="lazy"
            decoding="async"
            width="700"
            height="600"
          />
          <div class="absolute -bottom-6 -right-6 glass-panel p-8 rounded-xl z-20 border border-outline-variant/15 max-w-xs">
            <p class="font-headline text-primary text-4xl font-black italic">No Shortcuts.</p>
            <p class="text-sm text-on-surface-variant mt-2">
              Every single order is cooked from scratch when you press order. Real heat takes time.
            </p>
          </div>
        </div>
        <div>
          <SectionLabel text="The Story" />
          <h2 class="font-headline text-5xl font-extrabold mt-2 mb-8 leading-tight">
            A One-Man Show.<br /><span class="text-primary">Endless Passion.</span>
          </h2>
          <div class="space-y-6 text-on-surface-variant text-lg font-body">
            <p>
              Born from a kitchen in the heart of Kudla, Agni's is the result of a singular obsession: perfection through fire. We aren't a massive chain; we're a dedicated street kitchen focusing on what matters most—quality ingredients and explosive flavor.
            </p>
            <p>
              From our hand-picked coastal spices to our custom-blend burger patties, everything is handled by a single pair of hands. This ensures that every bite you take is exactly how it was meant to be: Fresh. Fiery. Authentic.
            </p>
          </div>
          <div class="mt-12 flex items-center gap-6">
            <div class="flex flex-col">
              <span class="text-3xl font-black text-on-surface font-headline">100%</span>
              <span class="text-xs uppercase tracking-widest font-bold text-secondary font-body">Fresh Ingredients</span>
            </div>
            <div class="w-[1px] h-12 bg-outline-variant/30" aria-hidden="true"></div>
            <div class="flex flex-col">
              <span class="text-3xl font-black text-on-surface font-headline">0</span>
              <span class="text-xs uppercase tracking-widest font-bold text-secondary font-body">Frozen Patties</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Social Proof -->
  <section class="py-24 px-6 container mx-auto">
    <div class="text-center mb-16">
      <h2 class="font-headline text-5xl font-extrabold">The Street Says It All</h2>
    </div>
    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
      <div class="space-y-4">
        <img src="/images/social-1.jpg" alt="Close up of a juicy burger with sauce dripping" class="w-full h-64 object-cover rounded-xl" loading="lazy" width="400" height="256" />
        <blockquote class="bg-surface-container-highest p-6 rounded-xl italic text-on-surface-variant font-body">
          "Juicy burgers that actually have flavor. Best in Mangalore hands down."
          <footer class="not-italic font-bold text-primary mt-4 text-sm">— Rahul K.</footer>
        </blockquote>
      </div>
      <div class="space-y-4 pt-12">
        <blockquote class="bg-surface-container-highest p-6 rounded-xl italic text-on-surface-variant font-body">
          "The fish fry takes me back to the coast instantly. Authentic spice level!"
          <footer class="not-italic font-bold text-primary mt-4 text-sm">— Priya S.</footer>
        </blockquote>
        <img src="/images/social-2.jpg" alt="Golden fried chicken pieces on dark background" class="w-full h-80 object-cover rounded-xl" loading="lazy" width="400" height="320" />
      </div>
      <div class="space-y-4">
        <img src="/images/social-3.jpg" alt="Seasoned fries in a basket" class="w-full h-80 object-cover rounded-xl" loading="lazy" width="400" height="320" />
        <blockquote class="bg-surface-container-highest p-6 rounded-xl italic text-on-surface-variant font-body">
          "Fresh and flavorful. You can taste the quality in the ingredients."
          <footer class="not-italic font-bold text-primary mt-4 text-sm">— Akhil M.</footer>
        </blockquote>
      </div>
      <div class="space-y-4 pt-12">
        <blockquote class="bg-surface-container-highest p-6 rounded-xl italic text-on-surface-variant font-body">
          "Finally, a place that doesn't hold back on the spice!"
          <footer class="not-italic font-bold text-primary mt-4 text-sm">— Shreya R.</footer>
        </blockquote>
        <img src="/images/social-4.jpg" alt="Plate of seafood and fries" class="w-full h-64 object-cover rounded-xl" loading="lazy" width="400" height="256" />
      </div>
    </div>
  </section>

  <!-- Location -->
  <section id="location" class="py-24 bg-surface-container">
    <div class="container mx-auto px-6">
      <div class="bg-surface-container-highest rounded-3xl overflow-hidden grid grid-cols-1 lg:grid-cols-5 border border-outline-variant/15">
        <div class="lg:col-span-2 p-12 flex flex-col justify-center">
          <SectionLabel text="Find Us" />
          <h2 class="font-headline text-4xl font-extrabold mt-2 mb-8">Fuel the Fire</h2>
          <address class="not-italic space-y-8">
            <div class="flex gap-4">
              <div>
                <h4 class="font-bold text-on-surface mb-1 font-body">Address</h4>
                <p class="text-on-surface-variant text-sm font-body">{siteData.address}</p>
              </div>
            </div>
            <div class="flex gap-4">
              <div>
                <h4 class="font-bold text-on-surface mb-1 font-body">Hours</h4>
                <p class="text-on-surface-variant text-sm font-body">{siteData.hours}</p>
              </div>
            </div>
            <div class="flex gap-4">
              <div>
                <h4 class="font-bold text-on-surface mb-1 font-body">Contact</h4>
                <a href={`tel:${siteData.phone}`} class="text-on-surface-variant text-sm font-body hover:text-primary transition-colors">{siteData.phone}</a>
              </div>
            </div>
          </address>
        </div>
        <div class="lg:col-span-3 min-h-[400px] relative">
          <img
            src="/images/map.jpg"
            alt="Map view of Mangaluru showing Agni's location"
            class="w-full h-full object-cover grayscale brightness-50 contrast-125"
            loading="lazy"
            width="900"
            height="400"
          />
          <div class="absolute inset-0 bg-primary/10 pointer-events-none" aria-hidden="true"></div>
          <div class="absolute inset-0 flex items-center justify-center" aria-hidden="true">
            <div class="bg-primary p-4 rounded-full animate-bounce shadow-[0_0_30px_rgba(255,143,112,0.5)]">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor" class="text-on-primary-fixed">
                <path d="M11 9H9V2H7v7H5V2H3v7c0 2.12 1.66 3.84 3.75 3.97V22h2.5v-9.03C11.34 12.84 13 11.12 13 9V2h-2v7zm5-3v8h2.5v8H21V2c-2.76 0-5 2.24-5 4z"/>
              </svg>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</BaseLayout>
```

**Step 2: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: add homepage with hero, top sellers, brand story, social proof, location"
```

---

## Task 7: Menu page (`/menu`)

**Files:**
- Create: `src/pages/menu.astro`

**Step 1: Create the menu page**

```astro
---
// src/pages/menu.astro
import BaseLayout from '../layouts/BaseLayout.astro';
import FoodCard from '../components/FoodCard.astro';
import SectionLabel from '../components/SectionLabel.astro';
import menuData from '../data/menu.json';
import siteData from '../data/site.json';

const allItems = menuData.categories.flatMap(cat => cat.items);
const featuredCombo = allItems.find(item => item.tags.includes('chef-special'));
---

<BaseLayout
  title={`Menu | ${siteData.name} Fish Chicken Fries`}
  description="Explore Agni's full menu — burgers, seafood, chicken, fries and combos. Coastal Mangalore flavors, made fresh to order."
  activePage="menu"
>
  <!-- Hero: Featured Combo -->
  {featuredCombo && (
    <section class="pt-24 mb-20 px-6 max-w-7xl mx-auto">
      <div class="relative w-full h-[500px] overflow-hidden rounded-xl bg-surface-container-highest group">
        <img
          src={featuredCombo.image}
          alt={featuredCombo.imageAlt}
          class="absolute inset-0 w-full h-full object-cover opacity-60 group-hover:scale-105 transition-transform duration-700"
          fetchpriority="high"
          decoding="async"
          width="1200"
          height="500"
        />
        <div class="absolute inset-0 bg-gradient-to-r from-background via-background/40 to-transparent"></div>
        <div class="relative h-full flex flex-col justify-center px-12 max-w-2xl">
          <SectionLabel text="Chef's Special" />
          <h1 class="font-headline text-6xl font-black tracking-tighter mb-4 text-on-surface leading-none mt-4">
            THE COASTAL<br /><span class="text-primary">EMBER FEAST</span>
          </h1>
          <p class="text-on-surface-variant text-lg mb-8 font-body leading-relaxed">{featuredCombo.description}</p>
          <div class="flex items-center gap-6">
            <a
              href={`https://wa.me/${siteData.whatsapp}?text=I'd like to order: ${encodeURIComponent(featuredCombo.name)}`}
              target="_blank"
              rel="noopener noreferrer"
              class="bg-primary text-on-primary-fixed font-black px-8 py-4 rounded-md text-lg tracking-tight hover:bg-primary-fixed-dim transition-all shadow-lg shadow-primary/10"
            >
              ORDER FOR {featuredCombo.currency}{featuredCombo.price}
            </a>
          </div>
        </div>
      </div>
    </section>
  )}

  <!-- Menu Grid -->
  <div class="flex flex-col md:flex-row gap-12 px-6 max-w-7xl mx-auto pb-32">
    <!-- Sidebar -->
    <aside class="md:w-64 flex-shrink-0">
      <nav class="sticky top-28 space-y-2" aria-label="Menu categories">
        <p class="font-headline font-black text-xs uppercase tracking-[0.3em] text-outline mb-6 px-4">Categories</p>
        {menuData.categories.map((cat, i) => (
          <a
            href={`#${cat.id}`}
            class={`group flex items-center justify-between px-4 py-4 rounded-md transition-all ${
              i === 0
                ? 'bg-surface-container-highest text-secondary border-l-4 border-secondary'
                : 'hover:bg-surface-container-low text-on-surface-variant hover:text-on-surface'
            }`}
          >
            <span class="font-bold tracking-tight font-headline">{cat.label.toUpperCase()}</span>
          </a>
        ))}
      </nav>
    </aside>

    <!-- Items -->
    <div class="flex-grow space-y-24">
      {menuData.categories.map(cat => (
        <section id={cat.id}>
          <div class="flex items-baseline space-x-4 mb-10">
            <h2 class="font-headline text-4xl font-black tracking-tighter text-on-surface">
              {cat.label.toUpperCase()}
            </h2>
            <div class="h-[2px] flex-grow bg-gradient-to-r from-outline-variant/30 to-transparent" aria-hidden="true"></div>
          </div>
          <div class={`grid gap-8 ${cat.items.length >= 3 ? 'grid-cols-1 md:grid-cols-2 lg:grid-cols-3' : 'grid-cols-1 md:grid-cols-2'}`}>
            {cat.items.map(item => (
              <FoodCard
                name={item.name}
                description={item.description}
                price={item.price}
                currency={item.currency}
                spiceLevel={item.spiceLevel as 1|2|3}
                tags={item.tags}
                image={item.image}
                imageAlt={item.imageAlt}
                layout="grid"
              />
            ))}
          </div>
        </section>
      ))}
    </div>
  </div>

  <!-- Floating WhatsApp CTA -->
  <a
    href={`https://wa.me/${siteData.whatsapp}`}
    target="_blank"
    rel="noopener noreferrer"
    class="fixed bottom-24 md:bottom-10 right-6 md:right-10 z-[60] flex items-center gap-3 bg-secondary text-on-primary-fixed px-6 py-4 rounded-full shadow-2xl shadow-secondary/20 hover:scale-105 active:scale-95 transition-all"
    aria-label="Order on WhatsApp"
  >
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
      <path d="M17.472 14.382c-.297-.149-1.758-.867-2.03-.967-.273-.099-.471-.148-.67.15-.197.297-.767.966-.94 1.164-.173.199-.347.223-.644.075-.297-.15-1.255-.463-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.298-.347.446-.52.149-.174.198-.298.298-.497.099-.198.05-.371-.025-.52-.075-.149-.669-1.612-.916-2.207-.242-.579-.487-.5-.669-.51-.173-.008-.371-.01-.57-.01-.198 0-.52.074-.792.372-.272.297-1.04 1.016-1.04 2.479 0 1.462 1.065 2.875 1.213 3.074.149.198 2.096 3.2 5.077 4.487.709.306 1.262.489 1.694.625.712.227 1.36.195 1.871.118.571-.085 1.758-.719 2.006-1.413.248-.694.248-1.289.173-1.413-.074-.124-.272-.198-.57-.347z"/>
      <path d="M12 0C5.373 0 0 5.373 0 12c0 2.124.554 4.122 1.526 5.858L0 24l6.266-1.502A11.94 11.94 0 0 0 12 24c6.627 0 12-5.373 12-12S18.627 0 12 0zm0 21.818a9.818 9.818 0 0 1-5.012-1.374l-.36-.214-3.72.892.926-3.618-.235-.372A9.818 9.818 0 1 1 12 21.818z"/>
    </svg>
    <span class="font-bold tracking-tight uppercase text-sm font-body hidden md:inline">Order on WhatsApp</span>
  </a>
</BaseLayout>
```

**Step 2: Commit**

```bash
git add src/pages/menu.astro
git commit -m "feat: add menu page with category sidebar and food grid"
```

---

## Task 8: Placeholder image assets

**Files:**
- Create: `public/images/` directory with placeholder images

**Step 1: Create placeholder image directory**

```bash
mkdir -p public/images
```

**Step 2: Add a `.gitkeep` and note**

Create `public/images/.gitkeep` with a comment:

```
# Replace these placeholder filenames with real food photography.
# Required images:
# hero-bg.jpg (1920x1080) — hero background
# brand-story.jpg (700x600) — chef/kitchen photo
# classic-burger.jpg (400x400)
# inferno-burger.jpg (400x400)
# citrus-fire-chicken.jpg (400x400)
# spicy-fish-fry.jpg (400x400)
# signature-fish.jpg (400x400)
# dynamite-prawns.jpg (400x400)
# chicken-tenders.jpg (400x400)
# dynamite-strips.jpg (400x400)
# peri-peri-fries.jpg (400x400)
# masala-dust-fries.jpg (400x400)
# volcano-loaded.jpg (400x400)
# coastal-ember-feast.jpg (1200x500)
# social-1.jpg through social-4.jpg (400x256 and 400x320)
# map.jpg (900x400) — static map screenshot of Mangaluru
# og-image.jpg (1200x630) — Open Graph share image
```

**Step 3: Commit**

```bash
git add public/images/
git commit -m "chore: add public/images directory with asset manifest"
```

---

## Task 9: Robots.txt and OG image placeholder

**Files:**
- Create: `public/robots.txt`
- Create: `public/og-image.jpg` (placeholder — replace with real OG image)

**Step 1: Create robots.txt**

```
User-agent: *
Allow: /

Sitemap: https://agnis-page.vercel.app/sitemap-index.xml
```

**Step 2: Commit**

```bash
git add public/robots.txt
git commit -m "chore: add robots.txt"
```

---

## Task 10: Vercel config + deploy

**Files:**
- Create: `vercel.json`
- Modify: `astro.config.ts` — confirm `site` URL matches actual Vercel project URL

**Step 1: Create `vercel.json`**

```json
{
  "buildCommand": "bun run build",
  "installCommand": "bun install",
  "framework": "astro"
}
```

**Step 2: Build locally to verify zero errors**

```bash
bun run build
```

Expected: `dist/` directory created, no errors, all pages pre-rendered to static HTML.

**Step 3: Push `development` branch to remote**

```bash
git push -u origin development
```

**Step 4: Deploy preview to Vercel**

```bash
bunx vercel --prod
```

Or connect the GitHub repo to Vercel dashboard (recommended — auto-deploys on push to `main`).

**Step 5: Verify deployment**

- Open the deployed URL
- Check `/sitemap-index.xml` exists
- Check `/robots.txt` exists
- Run Lighthouse audit in Chrome DevTools — target: Performance ≥ 95, SEO = 100, Accessibility ≥ 90

**Step 6: Commit vercel config**

```bash
git add vercel.json
git commit -m "chore: add vercel.json build config"
```

---

## Summary

| Task | What it builds |
|---|---|
| 1 | Astro 6 + Tailwind v4 + Vercel adapter scaffolded with Bun |
| 2 | Design system tokens in CSS `@theme` |
| 3 | `menu.json` + `site.json` content files |
| 4 | `BaseLayout` — nav, footer, mobile bottom bar |
| 5 | `FoodCard`, `SpiceIndicator`, `SectionLabel` components |
| 6 | Homepage — hero, top sellers, brand story, social proof, location |
| 7 | Menu page — featured combo hero, category sidebar, full item grid |
| 8 | Image asset manifest |
| 9 | `robots.txt` |
| 10 | Vercel config + build verification + deploy |
