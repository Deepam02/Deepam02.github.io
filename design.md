# Design document — `index.html`

Single-page portfolio for **Deepam Goyal** (AI Engineer). The page is self-contained: typography, layout, motion, and behavior live in one HTML file with embedded CSS and JavaScript.

---

## Design intent

- **Editorial + technical**: Large serif headlines and long-form body copy sit beside monospace labels, terminal chrome, and stack pills so the site reads as both *portfolio* and *engineer’s notebook*.
- **Warm minimalism**: Cream paper-like backgrounds, deep ink text, and rust accents avoid cold “startup gray” while staying readable and professional.
- **Motion with purpose**: Scroll-linked reveals, a typewriter hero terminal, and subtle cursor/spotlight effects reinforce craft without overwhelming content.

---

## Typography

| Role | Font | Usage |
|------|------|--------|
| Display / hero titles | **Instrument Serif** | `h1`, large numerals, high-impact lines |
| Section titles & card titles | **Cormorant Garamond** (300–400) | `h2`, project titles, quotes, body blurbs |
| UI / meta / labels | **DM Mono** (8–11px, wide letter-spacing, uppercase) | Nav links, section labels, tags, footer meta |
| Accent sans | **Syne** (600) | Body default; trait names on dark sections |

**Patterns**

- Section labels: ~9px mono, `letter-spacing` ~0.18–0.2em, uppercase, **rust** color.
- Headlines often use `<em>` for **italic rust** emphasis (not a separate display font).
- Hierarchy is size + weight + color opacity (e.g. secondary copy at ~55% ink on cream, ~50% cream on ink).

---

## Color system

CSS custom properties (see `:root` in `index.html`):

| Token | Hex / value | Role |
|-------|----------------|------|
| `--cream` | `#F7F2EA` | Default page background, light cards |
| `--ink` | `#18130F` | Primary text; dark section backgrounds |
| `--rust` | `#BF4A2B` | Primary accent, CTAs, links on hover |
| `--rust-light` | `#D46140` | CTA hover (nav) |
| `--sand` | `#E6DDD0` | Borders, dividers, subtle rules |
| `--warm-mid` / `--warm` | `#EDE6D8` | Alternate warm panels (OSS grid backdrop) |
| `--sage` | `#7A9178` | Secondary accent, “live” dots, success-ish states |
| `--gold` | `#C9A84C` | Stars, “open” availability emphasis |
| `--ink40` / `--ink15` | RGBA | Muted text and hairline atmosphere |

**Contrast**

- Dark bands (`#approach`, `#skillsB`, `#contact`, footer) invert to cream-on-ink; **rust** remains the accent on both themes.

---

## Layout & page structure

1. **Fixed nav** — Logo (name + rust period), anchor links (Approach, Work, Stack, OSS, Contact), primary CTA mailto.
2. **Hero** — Two-column grid (main copy + faux terminal). Decorative “01” numeral, eyebrow tag + meta, primary CTA + email copy button, scroll cue to `#approach`.
3. **Approach** — Full-bleed **ink** section: two-column header (title + blockquote), then numbered trait rows (mono index + Syne name + Cormorant description).
4. **Work (`#projectsA`)** — Horizontal snap track: internship cards, vertical divider pill (“Personal projects”), personal cards, dashed “+ more” cap card.
5. **Stack (`#skillsB`)** — Continuation of ink background; marquee-style rows of pills (`lit` / `dim`).
6. **OSS** — Warm grid background: hero two-column **docker/docker-agent** card, four secondary repo cards, three stat tiles. Expand/collapse regions for extra PRs and per-repo details.
7. **Contact** — Two columns: large headline + links; sticky **availability** card on cream.
8. **Footer** — Italic name line + uppercase mono tagline.

**Spacing**

- Section padding tends toward **48–64px** horizontal on desktop; vertical rhythm **100–140px** between major blocks.
- `scroll-margin-top` / `scroll-padding-top` align in-page anchors with the fixed nav (~88px).

---

## Components & surfaces

- **Cards (projects)**: White fill, 1px `sand` border, slight radius (~3px). Hover: lift, micro-rotation, shadow, circular arrow fills **rust**.
- **Terminal**: Dark rounded panel (`#1C1714`), traffic-light dots, syntax-colored lines (prompt / output / comment), blinking block cursor.
- **Buttons / links**: `btn-ink` (filled), `ncta` (rust pill in nav), `btn-ghost-lk` (underline grows on hover). Ghost email in hero swaps to “Copied ✓” with stacked grid transition.
- **OSS hero**: Split card — left narrative + PR list + toggle; right stats + external “View merged PRs” link.
- **OSS secondary**: Compact cards with star row, optional expandable description, tag + Details toggle.
- **Availability card**: Cream card on dark contact section; key-value rows; rust email button.

---

## Texture & atmosphere

- **Hero**: Faint 60×60px grid overlay on cream.
- **Ink sections**: Subtle 60px engineering grid in low-opacity cream.
- **OSS**: Similar grid in low-opacity ink on warm background.
- **Global**: Fixed radial **spotlight** following cursor (rust-tinted); very light **noise** overlay on `body::after`.
- **Custom cursor**: Hidden default cursor; dot + lagging ring scale on link/button hover.

---

## Motion & interaction

| Behavior | Implementation |
|----------|----------------|
| Section entrance | `.reveal` + `IntersectionObserver` adds `.in` (opacity + translateY) |
| In-page navigation | Custom `smoothScrollToY` with easing (~2.4s); instant jump if `prefers-reduced-motion` |
| Wheel scroll | Document wheel listener dampens vertical scroll (~0.52×) except inside scrollable regions and horizontal project track |
| Hero terminal | Timed typewriter building DOM rows from `tlines` |
| Project track | `scroll-snap-type: x mandatory`; drag-to-scroll via mousedown/mousemove |
| OSS toggles | `grid-template-rows: 0fr → 1fr` on `.oss-expand-wrap.open`; `aria-expanded` synced; click-outside collapse on expanded content |
| Nav shrink | `scrolled` class on `#nav-bar` after ~60px scroll |
| Live GitHub stars | `fetch` to GitHub API; formatted counts; HTML fallbacks if request fails |

**Micro-animations (CSS)**

- `riseIn` for hero stagger; `blink` for terminal cursor; `ticker` for skill marquees; `sonar` on status dots; scroll-cue line glow and traveling dot.

---

## Responsive behavior

| Breakpoint | Notable changes |
|------------|------------------|
| **≤1100px** | Hero single column; terminal below copy with top border; OSS hero stacks; nav padding tightens |
| **≤900px** | Approach / OSS headers and trait rows stack; contact single column; OSS stats stack; footer stacks centered; project horizontal padding reduces |
| **≤600px** | OSS secondary repo grid becomes single column |

Cards use `clamp` / `min()` so typography and hero scale fluidly before breakpoints.

---

## Accessibility & UX notes

- **Focus**: `:focus-visible` outlines use **rust** with offset on interactive elements.
- **Landmarks**: Semantic `nav`, `section`, `footer`; expand regions use `role="region"` and `aria-label` / `aria-expanded` / `aria-controls` where toggles exist.
- **Custom cursor**: `cursor: none` on `body` — users who rely on default cursor visibility may find this harder to use; consider respecting `prefers-reduced-motion` or a user setting to restore default cursor if you extend the site.
- **Reduced motion**: Smooth scroll and wheel damping bail out when `prefers-reduced-motion: reduce` is set; other CSS animations still run unless you add media-query guards later.

---

## Content & voice

Copy is **first-person, direct, and evidence-led** (concrete systems: K8s, Jira, RAG, merged PR counts). Section order mirrors a hiring narrative: positioning → how you work → shipped work → stack → public proof → contact.

---

## Dependencies & runtime

- **Google Fonts**: Cormorant Garamond, Instrument Serif, Syne, DM Mono (preconnect + stylesheet).
- **GitHub REST API** (unauthenticated): star counts for listed repos; CORS allows browser `fetch` from static hosting in typical setups.

---

## File map (conceptual)

| Region | HTML `id` / class | Primary styles (inline `<style>`) |
|--------|-------------------|-------------------------------------|
| Nav | `#nav-bar` | `#nav-bar`, `.nlink`, `.ncta` |
| Hero | `#hero` | `#hero`, `.mterm`, `.hero-scroll-cue` |
| Approach | `#approach` | `.trait-row`, `.approach-title` |
| Projects | `#projectsA` | `.pA-track`, `.pA-card` |
| Skills | `#skillsB` | `.smrow`, `.spill` |
| OSS | `#oss` | `.oss-hero`, `.oss-secondary`, `.oss-stats` |
| Contact | `#contact` | `.contact-grid`, `.avail-card` |
| Scripts | end of `<body>` | Cursor, reveal, scroll, wheel, email copy, terminal, drag, toggles, GitHub |

This document describes the design **as implemented** in `index.html` as of the current revision; keep it in sync when you change tokens, sections, or motion behavior.
