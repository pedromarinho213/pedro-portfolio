# Pedro Moura Portfolio — Project Brief
> Reference document for Claude Code. Read this before every session.
> **Source of truth for all design tokens is `src/styles/global.css`.** This
> brief documents and explains those tokens; the CSS file implements them.

---

## Project Overview

Personal portfolio for Pedro Moura, Motion & UI/UX Designer, graduating in Design at UFAM (Manaus, Brazil). The site replaces an outdated Lovable portfolio and establishes a professional online presence at **pedromoura.me**.

---

## Tech Stack

- **Framework:** Astro (static site generator)
- **Hosting:** GitHub Pages
- **Repo:** github.com/pedromarinho213/pedro-portfolio
- **Deploy:** GitHub Actions (auto-deploy on push to main)
- **Domain:** pedromoura.me (Namecheap via GitHub Student Pack)
- **Dev agent:** Claude Code + Figma MCP

---

## Token Architecture — READ THIS FIRST

All design tokens (colors, typography scale, spacing, radius, easing, durations) live in **one place**: `src/styles/global.css`, inside `:root`. This file is the single source of truth.

- `BaseLayout.astro` must **import** `global.css` once, and must **not** redeclare tokens or the CSS reset in its own `<style is:global>` block. If a duplicate `:root` / reset block still exists inside `BaseLayout.astro` from an earlier session, remove it — keep only the `<head>`, meta, font imports, and `<slot />` there.
- Never hardcode a color, font, spacing, or duration in any component. Always reference the token: `var(--color-bg)`, `var(--space-4)`, `var(--ease-reveal)`, etc.
- The values below are the **confirmed final values**. Where a value was verified via Figma MCP or manual eyedropper, it is marked. Trust these over any older approximation.

---

## Site Structure

```
pedromoura.me/                    ← home (single page scroll)
pedromoura.me/work/aura-finance   ← case study page
pedromoura.me/work/creatwave      ← case study page
pedromoura.me/work/mimo-doce      ← case study page
```

---

## Home Sections (in order)

1. Hero
2. Work
3. Skills
4. About
5. Contact

---

## Typography

| Role        | Font     | Weight   | Usage                          |
|-------------|----------|----------|--------------------------------|
| Headlines   | Roboto   | 700–800  | Hero headline, section titles  |
| Body / UI   | Poppins  | 400–500  | Subtitles, body text, labels   |

- Import **Roboto** (700, 800) and **Poppins** (400, 500) from Google Fonts in `BaseLayout.astro` (with `preconnect`).
- `--font-heading` = Roboto, applied to `h1`–`h6`.
- `--font-sans` = Poppins, applied to `body` (default).
- **`Rozha One` has been removed** — it is not used anywhere in the design. Do not import it or reference `--font-accent`.
- Note: global base sets `h1` to weight **700**. The Hero headline ("Design that moves.") needs weight **800** — override it locally in `Hero.astro`, don't change the global base.

---

## Visual Identity

### Colors — CONFIRMED FINAL VALUES

| Token                      | Value                    | Usage                                                        |
|----------------------------|--------------------------|--------------------------------------------------------------|
| `--color-bg`               | `#FFF9ED`                | Page background (creamy off-white)                           |
| `--color-text`             | `#353535`                | Headlines and body text                                      |
| `--color-text-muted`       | `#8C8479`                | Labels, tags, secondary text — warm grey (Figma-confirmed)  |
| `--color-nav-bg`           | `#353535`                | Floating nav pill background                                 |
| `--color-nav-text`         | `#FFFFFF`                | Nav link text — active state                                |
| `--color-nav-text-muted`   | `#B4B4B4`                | Nav link text — inactive/unselected state (MCP-confirmed)   |
| `--color-card-aura`        | `#B4C9FF`                | Aura Finance card — **PLACEHOLDER ONLY** (see note below)   |
| `--color-card-creatwave`   | `#6559A7`                | Creatwave card background — purple (MCP-confirmed)          |
| `--color-card-mimo`        | `#FFCCEB`                | Mimo Doce card background — pink                             |
| `--color-overlay`          | `rgba(0, 0, 0, 0.72)`    | Work card hover overlay                                      |
| `--color-surface`          | `rgba(53, 53, 53, 0.04)` | Subtle surface fill                                         |
| `--color-surface-raised`   | `rgba(53, 53, 53, 0.08)` | Raised surface fill                                        |
| `--color-border`           | `rgba(53, 53, 53, 0.15)` | Default border                                             |
| `--color-border-subtle`    | `rgba(53, 53, 53, 0.08)` | Subtle border                                             |

**Note on `--color-card-aura`:** The Aura Finance card is fully covered by a single mockup image (`object-cover`), with **no solid fill layer** behind it. `#B4C9FF` exists only as a loading/placeholder color for when the image is absent or still loading. In `Work.astro`, the Aura card's visual comes from the image, not from `background: var(--color-card-aura)`.

**Removed token:** `--color-text-subtle` no longer exists as a distinct token (it was just the main text color at low opacity, not a real design decision). ⚠️ The `::-webkit-scrollbar-thumb:hover` rule in `global.css` currently references `var(--color-text-subtle)` — change that reference to `var(--color-border)` (or `var(--color-text-muted)`) when reconciling the file, so nothing breaks.

### Gradient Trail — single trail, 4 stops

The Hero has **ONE single trail** (not two), rendered as a single SVG path with a **linear gradient of 4 stops**. Opacity varies per stop, so tokens are stored as `rgba()`, not hex.

| Token             | Value                      | Stop position | Source hex / opacity      |
|-------------------|----------------------------|---------------|---------------------------|
| `--color-trail-1` | `rgba(167, 86, 214, 1)`    | 0%            | `#A756D6` @ 100%          |
| `--color-trail-2` | `rgba(227, 143, 225, 1)`   | 32%           | `#E38FE1` @ 100%          |
| `--color-trail-3` | `rgba(63, 202, 228, 0.76)` | 68%           | `#3FCAE4` @ 76%           |
| `--color-trail-4` | `rgba(133, 173, 41, 0)`    | 97%           | `#85AD29` @ 0% (fade-out) |

Values came from manual Figma eyedropper — the trail layer is a flattened raster (blur/grain applied), so Figma MCP cannot expose the gradient stops. Treat these as authoritative.

The old `--color-accent-cyan` / `--color-accent-lavender` / `--color-accent-mint` tokens are **removed** — replaced by the four `--color-trail-*` tokens above.

### Grain/Texture
- Grain applied **only on the gradient trail**, not on the page background.
- Implement as an SVG `feTurbulence` filter on the trail path fill.
- Background stays flat and clean.

---

## Design System (from `global.css`)

These systems already exist in `global.css` and should be used throughout instead of magic numbers.

### Fluid type scale — `clamp(min, preferred, max)`
`--text-xs` · `--text-sm` · `--text-base` · `--text-lg` · `--text-xl` · `--text-2xl` · `--text-3xl` · `--text-4xl`
Scales smoothly between mobile and desktop without per-breakpoint font sizes.

### Line height / letter spacing
`--leading-none|tight|snug|normal|loose` and `--tracking-tighter|tight|snug|normal|wide|wider|widest`.

### Spacing (base-4)
`--space-1` (0.25rem) through `--space-48` (12rem).

### Border radius
`--radius-xs|sm|md|lg|xl|2xl|full`.

### Motion — easing curves (named by intent)
- `--ease-reveal` — content entering the viewport (long tail, no bounce)
- `--ease-out-expo` — standard exit
- `--ease-out-quart` — snappy entry, aggressive deceleration
- `--ease-in-expo` — entrance from rest
- `--ease-in-out-expo` — symmetrical
- `--ease-spring` — subtle overshoot (tactile feedback)
- `--ease-hover` — micro-interactions (hover/focus/toggle)
- `--ease-elastic` — pronounced overshoot (playful moments)

### Transition durations
`--duration-instant` (80ms) · `--duration-fast` (150ms) · `--duration-base` (260ms) · `--duration-slow` (500ms) · `--duration-slower` (800ms) · `--duration-crawl` (1200ms).

### Accessibility (already in global.css)
- `prefers-reduced-motion` block neutralizes animations/transitions — satisfies the animation requirement in this brief.
- `:focus-visible` outline, custom `::selection`, custom scrollbar.

---

## Section Specs

### Hero
- Background: flat `--color-bg`, no grain.
- **One** organic S-curve gradient trail (single SVG path) with the 4-stop linear gradient defined above, grain texture applied to its fill via SVG filter. The trail spans the whole Hero background (enters top-left, weaves down/right, fades out at the last stop).
- Glassmorphism blur **only** behind the headline text (`backdrop-filter: blur(12px)` + semi-transparent wrapper `background: rgba(255,249,237,0.3)`), nowhere else on the site.
- Nav pill: dark background (`--color-nav-bg`), centered top, links: Home · Works · Contact.
- Logo "Pedro" top-left corner, small, dark (`--color-text` / `--color-nav-bg`).
- Headline: `"Design that moves."` — Roboto **800** (override global h1), very large (`--text-4xl`), center-aligned.
- Subtitle: `"Pedro Moura — UI/UX & Motion Designer"` — Poppins Regular.
- Short body text below subtitle (2 lines max).
- "Scroll to explore" indicator with arrow icon, centered.

### Work
**Desktop (≥1024px):**
- Horizontal card grid, all cards same height.
- Featured card (Aura Finance): large/wide, expanded by default — shows the mockup **image** (fills the card, `object-cover`) + category label bottom-left + "View case" pill CTA bottom-right. No solid CSS background; the image is the visual.
- Secondary cards (Creatwave, Mimo Doce): narrower, solid color background (`--color-card-creatwave` / `--color-card-mimo`) with project logo centered.
- Hover on secondary cards: dark overlay (`--color-overlay`) reveals title, tags, CTA.

**Mobile (<1024px):**
- Vertical accordion.
- Each row: project name + category tag + expand/collapse toggle (`+` / `−`).
- Expanded state: project preview placeholder + "View case →" CTA.

**Projects:**
| # | Name | Category | Card visual |
|---|------|----------|-------------|
| 01 | Aura Finance | Mobile/Web App & Motion Design | mockup image (placeholder color `--color-card-aura`) |
| 02 | Creatwave | Branding & Motion Design | `--color-card-creatwave` |
| 03 | Mimo Doce | UI/UX Design | `--color-card-mimo` |

Section label: `"Best Projects"` with counter `"01 – 03"` on the right.

### Skills
- Section label: `"Tools and skills"`.
- Horizontal icon row — real app icons (PNG/SVG):
  - After Effects
  - Premiere Pro
  - Figma
  - Animaker (or equivalent)
  - Claude / AI tools (asterisk/snowflake icon)
- Hover state: icon expands (`scale(1.15)`) + skill name fades in below.
- **Do not simplify to static version — hover interaction is intentional and must be preserved.**
- Mobile: horizontal scroll, no hover (show label statically below icon).

### About
- Section label: `"About me"`.
- Desktop: two columns — illustrated avatar left, text right.
- Mobile: single column — avatar top, text below.
- Subheadline: `"A blend of motion, logic, and visual art."` — Poppins italic.
- Body: `"Design student at UFAM turning complex problems into clean, moving interfaces. Bridging aesthetics and usability, one frame at a time."` — Poppins Regular.

### Contact
- Headline: `"Let's work together!"` — Roboto Bold, very large, centered.
- Three text links with arrow icons, centered row:
  - `linkedin ↗`
  - `instagram ↗`
  - `email ↗`
- No form, no button — links only.

---

## Navigation

**Desktop:**
- Floating dark pill (`--color-nav-bg`), centered top of page.
- Links: `Home · Works · Contact` — Poppins, light weight.
- Active link: `--color-nav-text` (`#FFFFFF`). Inactive links: `--color-nav-text-muted` (`#B4B4B4`).

**Mobile:**
- Bottom tab bar with icons — `Home · Works · Contact`, fixed to bottom of viewport.

**Both breakpoints:**
- Logo `"Pedro"` always top-left, small, dark.
- Nav stays sticky during scroll.

---

## Animations & Interactions

### Gradient trail
- Animate on section entry via IntersectionObserver (not continuous scroll).
- Entry animation: `opacity: 0 → 1` + `transform: translateY(20px) → translateY(0)`, using `--ease-reveal`.
- Fallback: SVG `animateMotion` (GPU-rendered).

### Work cards hover (desktop only)
- Scale: `transform: scale(1.02)` on card.
- Overlay: `background: rgba(0,0,0,0) → var(--color-overlay)`.
- Content: title + tags + CTA `translateY(12px) → translateY(0)` + `opacity: 0 → 1`.
- Duration: `--duration-base` (~260ms) region; brief originally specced 350ms — tune to taste with `--ease-out-quart`.

### Skills hover (desktop only)
- Icon: `transform: scale(1) → scale(1.15)`.
- Label: `opacity: 0 → 1` + `translateY(4px) → translateY(0)`.
- Duration: `--duration-base`, easing `--ease-hover`.

### General scroll animations
- All sections: `opacity: 0 → 1` + `translateY(20px) → translateY(0)` on entry.
- IntersectionObserver threshold: 0.15.
- `prefers-reduced-motion` is already handled globally in `global.css`.

---

## Breakpoints

| Name    | Width  |
|---------|--------|
| Mobile  | 390px  |
| Desktop | 1440px |

**Desktop-first.** Mobile adaptation follows after desktop is complete.

---

## Implementation Order

1. ✅ `BaseLayout.astro` — head, meta, Google Fonts (Roboto + Poppins), `<slot />`. **Must import `global.css`; must not redeclare tokens/reset.**
2. `Nav.astro` — floating pill (desktop) + bottom tab bar (mobile)
3. `Hero.astro` — single SVG trail (4-stop gradient + grain) + blur wrapper + headline (800) + scroll indicator
4. `Work.astro` — card grid (desktop) + accordion (mobile)
5. `Skills.astro` — icon row + hover interaction
6. `About.astro` — two-column layout
7. `Contact.astro` — links section
8. `index.astro` — assembles all sections in order
9. `/work/[slug].astro` — dynamic case study pages
10. IntersectionObserver scroll animations
11. Mobile QA + final adjustments

---

## Case Study Pages

Each project at `/work/[project-name]` — placeholder-first approach.

Structure per page:
- Hero: project name + category + year
- Overview: challenge + role
- Visual showcase: full-width images / video
- Details: tools, timeline, outcomes
- Back link to home

---

## Assets Location

```
/public/
  /assets/
    /images/     ← project screenshots, avatar
    /videos/     ← project loop videos (added later)
    /fonts/      ← only if self-hosting
    /icons/      ← skill icons as SVG
  /trails/       ← gradient trail SVG (single trail, inline or component)
```

---

## Notes for Claude Code

- **`global.css` is the single source of truth for tokens.** Import it once in `BaseLayout.astro`; never duplicate tokens elsewhere.
- Always use CSS custom properties — never hardcode colors, fonts, spacing, radius, or durations.
- Roboto (700/800) + Poppins (400/500) only. No Rozha One.
- The Hero trail is **one** SVG path with a 4-stop linear gradient + `feTurbulence` grain filter.
- Hero blur: wrap headline in a `<div>` with `backdrop-filter: blur(12px)` and `background: rgba(255,249,237,0.3)`.
- Aura Finance card = mockup image, not a solid color. `--color-card-aura` is only a placeholder.
- Skills hover is desktop-only — scope with `@media (hover: hover)`.
- All Astro components use scoped `<style>` blocks (component-specific styles only; shared tokens come from `global.css`).
- Fix the `--color-text-subtle` reference in the scrollbar rule (token removed).
- When building a component that uses trail/card/text colors, the confirmed values above are authoritative — no need to re-query the MCP for them.
