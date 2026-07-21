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

- Import **Roboto** (400, 500, 600, 700, 800), **Poppins** (300, 400, 500, 600, plus 400 italic), and **Rozha One** (400) from Google Fonts in `BaseLayout.astro` (with `preconnect`). The 400/500/600 Roboto weights and 300 Poppins weight were added for the Creatwave case study page (`Roboto` project titles/headings, `Poppins Light` body copy) — see Case Study Pages section.
- `--font-heading` = Roboto, applied to `h1`–`h6`.
- `--font-sans` = Poppins, applied to `body` (default).
- **Correction — `Rozha One` is back in the design, as a narrow, deliberate exception.** An earlier brief revision said it had been fully removed; re-checking the live Figma file via MCP (2026) showed it's still used for the word **"me"** in the "About me" heading — both desktop (48px) and mobile (32px) — set directly against Poppins Medium for "About ". It is **not** wired up as `--font-accent` or any other global token; it's applied as a one-off `font-family: 'Rozha One', serif;` scoped to `About.astro`'s heading accent span only. Do not reach for it anywhere else without checking Figma first — this is the one confirmed exception, not a reopened general-purpose font.
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
| `--color-media-placeholder`| `#D9D9D9`                | Case study image/video placeholders (Figma-confirmed, reused across all case study media slots until real assets exist) |
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
- Background: flat `--color-bg`, no grain (the flat/no-grain part applies to the page background — the trail itself always carries grain, see below).
- **One** organic S-curve gradient trail (single SVG path) with the 4-stop linear gradient defined above, grain texture applied to its fill via SVG filter. The trail spans the whole Hero background (enters top-left, weaves down/right, fades out at the last stop).
- **Correction — the trail is not home-exclusive.** An earlier brief revision implied it was a home-Hero-only decoration; the Figma file shows the same trail treatment at the top of the case study pages too (confirmed via MCP on the "Creatwave detalhe project" frame — same `--color-trail-*` gradient + `feTurbulence` grain, same source path/viewBox as the home Hero, just a shorter container so it fades out sooner, around the project title instead of filling a full 100vh). Reuse the exact same SVG markup/tokens per case study page; see Case Study Pages section for how `creatwave.astro` sizes the container.
- Glassmorphism blur **only** behind the headline text (`backdrop-filter: blur(12px)` + semi-transparent wrapper `background: rgba(255,249,237,0.3)`), nowhere else on the site.
- Nav pill: dark background (`--color-nav-bg`), centered top, links: Home · Works · Contact.
- Logo "Pedro" top-left corner, small, dark (`--color-text` / `--color-nav-bg`).
- Headline: `"Design that moves."` — Roboto **800** (override global h1), very large (`--text-4xl`), center-aligned.
- Subtitle: `"Pedro Moura — UI/UX & Motion Designer"` — Poppins Regular.
- Short body text below subtitle (2 lines max).
- "Scroll to explore" indicator with arrow icon, centered.

### Work
**Desktop (≥1024px):**
- Três cards com **largura igual** por padrão (não há card "featured" fixo mais).
- Estado de repouso: cada card mostra apenas o logo/nome do projeto centralizado sobre a cor sólida correspondente (`--color-card-aura` / `--color-card-creatwave` / `--color-card-mimo`).
- **Interação de hover (expand-on-hover):** ao passar o mouse sobre qualquer card, ele expande em largura (os outros dois encolhem proporcionalmente para abrir espaço) e revela: preview em vídeo/gif do projeto, categoria/tags, e CTA "View case" — tudo com transição suave (`--duration-base` a `--duration-slow`, `--ease-out-quart`).
- Ao tirar o mouse, o card volta ao estado de repouso (larguras iguais).
- **Placeholder de vídeo:** enquanto os assets de vídeo/gif não estiverem prontos, usar um retângulo com a cor do overlay (`--color-overlay`) + ícone de play como placeholder visual, no lugar do vídeo real.

**Mobile (<1024px):**
- Vertical accordion (comportamento inalterado).
- Each row: project name + category tag + expand/collapse toggle (`+` / `−`).
- Expanded state: project preview placeholder + "View case →" CTA.

**Projects:**
| # | Name | Category | Card visual |
|---|------|----------|-------------|
| 01 | Aura Finance | Mobile/Web App & Motion Design | `--color-card-aura` (placeholder até imagem/vídeo real) |
| 02 | Creatwave | Branding & Motion Design | `--color-card-creatwave` |
| 03 | Mimo Doce | UI/UX Design | `--color-card-mimo` |

Section label: `"Best Projects"` with counter `"01 – 03"` on the right.

### Skills
- Section label: `"Tools and skills"`.
- Horizontal icon row — real app icons, exported as SVG via Figma MCP from the "compilado de icones" layer group, stored in `/public/assets/icons/`:
  - After Effects
  - Premiere Pro
  - Figma
  - **Affinity** (corrected — the original brief said "Animaker (or equivalent)"; the tool actually used and confirmed in Figma is **Affinity**, not Animaker)
  - Claude / AI tools
- **Brand icon colors are a deliberate exception to the token system** — these SVGs keep their real logo colors (hardcoded per-icon `fill`), not `--color-*` tokens, since they represent third-party brand marks.

**Desktop (≥1024px) — marquee with pause-on-hover:**
- The marquee's visible area (`.skills-marquee`, `overflow: hidden`) is a **fixed-width window centered in the section** (`max-width: 45rem`, `margin-inline: auto`) — it does **not** stretch to the section's full width. The icon row itself is wider than this window and slides through it. A full-width window with only 5 icons made the repeated icons read as "many different tools" instead of a loop of the same 5 — narrowing the window (plus the larger icon size/gap below) fixes that.
- Icon spacing: `var(--space-24)` between icons (increased from the initial `--space-16` — icons read as too tightly packed at the smaller value, reinforcing the "repeated icons" confusion). Icon display size: `5rem`.
- **Spacing implementation — item margin, not container `gap`:** the spacing is set as `margin-right: var(--space-24)` on each `.skill-item`, **not** a `gap` on `.skills-list`. This matters because the track is built from **two separate `<ul>` elements** (the two halves); a `gap` on `.skills-list` only spaces items *within* one half, leaving zero space at the seam between the last item of half 1 and the first item of half 2 — which read as two icons "stuck together" every time the loop reset. Giving every item (including the last one in each half) its own trailing margin makes the seam identical to every other icon pair.
- **Duplication technique for a seamless loop:** the 5-skill array is rendered as two identical "halves" back-to-back in the DOM (**10 icons total, one copy of the array per half** — not repeated within each half), and the track animates `translateX(0 → -50%)` (exactly the width of one half). This exact-50% math only holds if the two halves are truly identical repeating units — see the margin-vs-gap note above; a `gap` added at the `.skills-track` level (between the two `<ul>`s) instead of per-item breaks the 50% split, since it doesn't distribute evenly across both halves.
- **Why one copy per half is enough here:** the rule is that a single half's rendered width (5 icons + each icon's own trailing margin) must be ≥ the marquee window's width, or a gap/jump appears right before the loop resets. At `5rem` icons + `--space-24` trailing margins, one half ≈ 880px, comfortably wider than the 45rem (720px) window. If the window is later widened past a half's rendered width, go back to repeating the array within each half (or add more skills) rather than shrinking the gap/icon size back down.
- `animation-timing-function: linear` (constant speed, no easing — deliberately different from the rest of the site's motion vocabulary), `17.3s` per cycle (~1.5× the original 26s speed).
- The second half of the DOM is marked `aria-hidden="true"` with empty `alt=""` on its icons, so screen readers only announce each tool name once.
- On hover over the Skills section, the marquee **pauses** (`animation-play-state: paused`). Individual icon hover (`scale(1.15)` + name fade-in `opacity/translateY`) only then becomes the dominant interaction — both are scoped under `@media (hover: hover)`, desktop-only.
- **Hover-scale headroom:** `.skills-marquee` has `padding-block: var(--space-4)` so the `scale(1.15)` icon hover has room to grow without its top edge being clipped by the marquee's `overflow: hidden` — transforms don't affect layout size, so without this padding the enlarged icon exceeds the container's natural (unscaled) height.
- **Do not simplify to static version — hover interaction is intentional and must be preserved.**

**Mobile (<1024px):**
- No marquee, no hover. Static row, manual horizontal scroll (`overflow-x: auto`).
- Tool name always visible below each icon, no interaction required.

### About
- **Correction (Figma-confirmed via MCP):** the "About me" heading is **not** the small `[data-label]` overline style used by "Best Projects" / "Tools and skills" — an earlier brief revision said it should match that pattern, which was wrong. It's a real heading: centered, Poppins Medium, `var(--text-2xl)`, with the word **"me" set in Rozha One** (see Typography section) — `"About "` in Poppins, `"me"` in Rozha One, same line, same size.
- Desktop (≥1024px): avatar left, text block right, vertically centered, `gap: var(--space-32)`. Avatar `18.5rem` (Figma: 295px).
- **Correction — mobile is not a stacked single column.** The live "mobile - 390 px" Figma frame shows avatar and text **side by side** at every breakpoint, just with a much smaller avatar (Figma: 114px → `7.125rem`) and tighter gap (`var(--space-8)`). Only the avatar/gap sizes change at `<1024px`; `flex-direction` stays `row` throughout.
- **Avatar shape:** a "squircle", not a circle and not `--radius-2xl`. Figma measures corner radius at ~33% of the avatar's own width at both breakpoints (99px/295px desktop, 38px/114px mobile) — doesn't match any `--radius-*` token (they max out well below this ratio, or jump straight to `--radius-full`/circle). Implemented as `border-radius: 33%` directly on `.about-avatar`, documented inline since it's an intentional exception to "always use a token".
- Subheadline: `"A blend of motion, logic, and visual art."` — Poppins italic, `var(--text-lg)`, `var(--color-text)`.
- Body: `"Design student at UFAM turning complex problems into clean, moving interfaces. Bridging aesthetics and usability, one frame at a time."` — Poppins Regular (not italic — Figma's mobile layer confirms this explicitly; desktop's layer grouping was ambiguous on this point), `var(--text-base)`, `var(--color-text)`.

### Contact
- **Correction (Figma-confirmed via MCP):** headline font is **Poppins SemiBold**, not Roboto Bold as an earlier brief revision said.
- Headline: `"Let's work together!"` — Poppins SemiBold, `var(--text-3xl)`, centered. `margin-bottom: var(--space-20)` to the links row (Figma: exact 80px match at desktop).
- Three text links with arrow icons:
  - `linkedin ↗`
  - `instagram ↗`
  - `email ↗`
- Desktop (≥1024px): centered horizontal row, `gap: var(--space-16)`.
- Mobile (<1024px): stacked column, `gap: var(--space-24)` (Figma: 97px, near-exact match).
- Arrow icon: Figma's `tdesign:arrow-up` symbol, exported once via MCP and reused identically on all three links (the source file has two slightly different implementations — a pre-angled asset for linkedin vs. a straight-up icon rotated 25.51° for instagram/email — both landing on the same visual angle; simplified here to one clean SVG, no extra rotation needed since the exported vector is already diagonal).
- Link hrefs are placeholders (`href="#"`) — no real profile URLs yet.
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
- **Link targets — fixed to work from any page, not just the home scroll.** `Nav.astro` originally hardcoded bare hash hrefs (`#home`, `#works`, `#contact`, and the logo too) that only resolve correctly while already on `/` — from a case study page, `#home` matches no element on that page and the browser doesn't scroll where expected. Fixed: `Home` and the logo link to `/` (a real page, not a section — no hash needed). `Works` and `Contact` link to `/#works` / `/#contact` — same-document smooth-scroll when already on `/`, full navigation to `/` followed by the browser's own scroll-to-hash when coming from elsewhere. Don't reintroduce bare `#id` hrefs in `Nav.astro`.

---

## Animations & Interactions

### Gradient trail
- Animate on section entry via IntersectionObserver (not continuous scroll).
- Entry animation: `opacity: 0 → 1` + `transform: translateY(20px) → translateY(0)`, using `--ease-reveal`.
- Fallback: SVG `animateMotion` (GPU-rendered).

### Work cards hover — expand-on-hover (desktop only)
- Repouso: os 3 cards com largura igual (`flex: 1` cada), mostrando apenas logo/nome centralizado.
- Hover: o card sob o mouse expande em largura (ex.: `flex-grow` maior), os outros dois encolhem proporcionalmente para abrir espaço — como uma galeria interativa.
- Ao expandir, revela: preview de vídeo/gif (ou placeholder com `--color-overlay` + ícone de play), categoria/tags, e CTA "View case", com `opacity: 0 → 1` + `translateY(12px) → translateY(0)`.
- Ao tirar o mouse, o card volta à largura igual dos demais.
- Duration: `--duration-base` a `--duration-slow`, easing `--ease-out-quart`.

### Skills marquee + hover (desktop only)
- Marquee: continuous `translateX` loop, `linear` timing, infinite, ~26s/cycle — see Section Specs > Skills for the duplication technique.
- Pause on hover: `animation-play-state: paused` when the mouse is over the Skills section.
- Icon (while paused, on individual icon hover): `transform: scale(1) → scale(1.15)`.
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

Each project lives at a **static** route, `src/pages/work/<slug>.astro` (e.g. `creatwave.astro`) — no `[slug].astro` dynamic route yet. `creatwave.astro` is the reference implementation and doubles as the template for `aura-finance.astro` / `mimo-doce.astro` once their copy exists: **all content/metadata/palette/media lives in one typed `project` object at the top of the file** (`CaseStudyData` interface — title, subtitle, `meta[]`, one entry per content section with `heading`/`body`/`media`, `palette.colors[]`, `nextCase`). The markup below only reads from that object. To spin up the next case study, copy the file and replace the object — don't touch the JSX/markup or the `<style>` block unless the layout itself needs to change.

**Section order (Figma-confirmed via MCP, frames "Creatwave detalhe project" / "... mobile"):**
1. **Hero** — decorative gradient trail (same as home Hero, see Section Specs > Hero) behind the Nav, fading out around the title → media placeholder (full-width, **`aspect-ratio: 16 / 9`** — deliberately not Figma's exact hero crop, since this slot is a future video export at 1920×1080 and 16:9 avoids a layout reflow when it drops in) → title + subtitle (side-by-side row, bottom-aligned, desktop; stacked, mobile) → meta row (`Role` / `Tools` / `Timeline` / `Context`, row on desktop, stacked on mobile).
2. **Reading the Market** — image (left) + heading/body (right) on desktop; image then text, stacked, on mobile. Both breakpoints: image before text in the DOM.
3. **Choosing the Right Direction** — heading centered, full-width image, body centered below — same centered single-column structure at both breakpoints.
4. **A Palette Built on Intention** — heading/body/palette-list (left) + image (right) on desktop; same order stacked on mobile (image comes **last** here, opposite of section 2 — confirmed per-breakpoint via MCP, not assumed).
5. **Motion & Behavior** — full-width image then body. **No visible heading in Figma for this section** (the frame only has an image + description, no title layer) — the "Motion & Behavior" name is this brief's/the page's internal label only (used as the section's `id`), not rendered on screen. Don't add a fake heading here to fill out the pattern.
6. **Next Case** — label ("Next Case →") + next project name (left/top) + "View next case" pill button with an arrow icon (right/bottom) — reuses `Work.astro`'s CTA pill styling exactly (`--color-nav-bg` / `--color-nav-text` / `--radius-full`), per this task's explicit instruction, even though Figma's own button layer uses a slightly different color (`#f5f1e8`) and a font (`DM Sans`) not used anywhere else in the project — both treated as one-off Figma inconsistencies, not new tokens.

**Normalization decisions (where Figma's numbers were noisy or the prompt said "Figma manda" but the source itself had inconsistencies):**
- **One consistent page container**, `max-width: 76.5rem` (1224px, matching the Hero's content width), used for **every** section — Figma's per-section frames actually use inconsistent left/right margins (e.g. the Market section is offset 212px left / 108px right, not symmetric) which reads as an authoring slip rather than a deliberate per-section width; a single reusable container is more correct for a template meant to be copied.
- **Body/heading colors normalized to `var(--color-text)`** — several Figma text layers in this page use raw `text-black` or `#1c1915` instead of the site's `#353535` token (the same "unfinished layer color" pattern seen earlier in `About.astro`/`Contact.astro`). Treated as unintentional, not a deliberate case-study-specific palette choice.
- **Meta tag label color `#8c8479` already equals `var(--color-text-muted)` exactly** — no new token needed there.
- Media placeholder `#D9D9D9` is genuinely reused 5 times across this page (hero + 4 section media) — added as a real token, `--color-media-placeholder`, rather than repeated as a bare hex (see Colors table). Each placeholder's `aspect-ratio` is set per section per breakpoint from the actual Figma frame dimensions (they vary a lot — e.g. the "Choosing the Right Direction" image is landscape on desktop, portrait on mobile).
- Font sizes mapped to the nearest fluid `--text-*` token rather than copied as fixed px (same approach as every other component) — case study body copy (`Poppins Light`, 20px desktop / 15px mobile) landed on `var(--text-base)`; section headings (`Roboto Medium`, 40px desktop / 32px mobile) landed on `var(--text-2xl)`; the page's `<h1>` project title (`Roboto SemiBold`, 64px) uses `var(--text-3xl)`, not the global `h1` default of `--text-4xl`/800 — that weight/size is reserved for the home Hero, this is a subpage title.
- **Palette colors are local to `creatwave.astro`'s data object, not global tokens** — they belong to the Creatwave brand shown *in* the case study, not to this site's own design system. Each future case study's palette section will declare its own colors the same way.
- **Correction — the hero trail is present on this page.** An earlier revision of this brief said case study pages stay flat/no-trail; that was wrong (see Section Specs > Hero). The rest of the page (everything below the hero) stays flat `--color-bg`, no grain — only the hero region gets the trail. Scroll-entry animation is still deferred (unified IntersectionObserver pass later, across every section of every page) — the trail renders statically visible, no entrance transition, unlike the home Hero's `.hero-trail.is-visible` IntersectionObserver toggle.
- **Trail sizing:** `.case-trail` is an absolutely positioned, `overflow: hidden` box at the top of `.case-hero`, `height: 44rem` desktop / `32rem` mobile. This is a hard clip, not a soft estimate — it's set to the exact bottom edge of Figma's "Hero video-placeholder" (705px desktop / 509px mobile; MCP-confirmed on nodes `38:75` / `45:43`), which sits well before "Head title and subtitle" starts (y=769 / y=549). The trail can never reach the title/meta text, regardless of how the gradient's own color/opacity falls. (An earlier pass had sized this off the trail SVG's own bounding box instead — ~852px/586px — which overshot into the title area; use the media-placeholder bottom edge as the reference, not the vector's bounding box.)
- **Trail edge fade — `trail-fade` shared utility (`global.css`):** a hard `overflow: hidden` cutoff left a visible straight line at the bottom of the trail. Fixed with a `mask-image`/`-webkit-mask-image` (`linear-gradient(to bottom, black 0%, black var(--trail-fade-start), transparent 100%)`) layered on top of the clip — `overflow: hidden` stays as the actual safety boundary, the mask is a purely visual smoothing pass so the trail dissolves before the edge instead of being cut there. Defined once in `global.css` as `.trail-fade` (default `--trail-fade-start: 60%`) so every case study page reuses the same class instead of redefining the gradient per file; each page overrides `--trail-fade-start` on its own `.case-trail` per breakpoint if its container proportions call for it — `creatwave.astro` uses `55%` desktop / `45%` mobile (shorter mobile container fades sooner, proportionally). Apply as `class="case-trail trail-fade"`.

**Font weights added for this page:** `Roboto` needed 400 (next-case project name) and 500 (section headings) in addition to the existing 700/800; `Poppins` needed 300/Light (all case-study body copy, plus the hero subtitle) in addition to the existing 400/500/600/italic. `BaseLayout.astro`'s Google Fonts import was updated accordingly — see Typography section.

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
- Roboto (700/800) + Poppins (400/500/600, incl. italic) are the workhorse fonts. Rozha One is a single confirmed exception — see Typography section — used only for the "me" accent in About.astro's heading. Don't extend it elsewhere without a fresh Figma check.
- The Hero trail is **one** SVG path with a 4-stop linear gradient + `feTurbulence` grain filter.
- Hero blur: wrap headline in a `<div>` with `backdrop-filter: blur(12px)` and `background: rgba(255,249,237,0.3)`.
- Aura Finance card = mockup image, not a solid color. `--color-card-aura` is only a placeholder.
- Skills hover is desktop-only — scope with `@media (hover: hover)`.
- All Astro components use scoped `<style>` blocks (component-specific styles only; shared tokens come from `global.css`).
- Fix the `--color-text-subtle` reference in the scrollbar rule (token removed).
- When building a component that uses trail/card/text colors, the confirmed values above are authoritative — no need to re-query the MCP for them.
