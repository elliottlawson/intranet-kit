---
name: infinite-loop
display: Infinite Loop
mode: template
template: templates/infinite-loop.html
config: themes.config.json
inspiration: apple.com store category listing
---

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `build-themes` skill.

## Config slots

- `services[].name`, `url` — verbatim.
- `services[].group` — one of `create`, `watch`, `read`, `keep` (the segmented filter).
- `services[].tagline` — one line in Apple voice, generated from the service fact. Period-terminated.
- `services[].art` — `{"type":"img","src":"<data-URI>"}` for a real product mark (waterfall: service favicon → dashboard-icons → Simple Icons), or `{"type":"svg","svg":"<inline svg>"}` for a designed default stroke mark. Marks are locked as data-URIs/inline SVG in the config — never hotlinked.

The footer count renders live. Card structure (art box, name, tagline, Open pill) is fixed.

# Apple

## Essence
A storefront for the services — an Apple Store category listing, compressed for an intranet launcher. Every service is a product in the lineup. The look is a working structure: the "Explore the lineup." header, a segmented filter control, and a horizontal slider of product cards. Hierarchy lives in the filter and the header, never in fake featured slots. If the header fills the first viewport by itself, the theme has failed.

## Palette
- paper: #F5F5F7 — page background
- card: #FFFFFF — the product image box, nothing else
- ink: #1D1D1F — primary text, never pure black
- muted: #6E6E73 — taglines, fine print
- link: #0066CC — the Open pill only
- seg: #E8E8ED — segmented control track and arrow buttons

## Type
- Stack: `"SF Pro Display", "SF Pro Text", -apple-system, Helvetica Neue, sans-serif`.
- Header: "Explore the lineup." — 56px, weight 600, tracking -0.02em. Apple idiom: headline ends with a period.
- Card service name: 28px weight 600, centered.
- Tagline: 17px muted, centered.
- Open pill: 17px white on link-blue.
- Fine print: 12px muted.

## Geometry
- The only white surface is the product image box: 28px radius, ~220px tall, logo centered inside with generous air.
- Name, tagline, and Open pill sit directly on the gray background below the image box — never inside it.
- Open pill: 980px radius, solid link-blue, white text, centered.
- Segmented control: one seg-gray pill container, options inside; the active option is a solid ink pill with white text.
- Slider arrows: two 44px seg-gray circles with chevrons, bottom-right. Disabled state at 35% opacity.

## Layout grammar
- Header (~64px top padding) → segmented control → horizontal slider → arrows bottom-right → fine-print footer.
- Slider: cards 320px wide, 20px gaps, scroll-snap, hidden scrollbar. Arrows scroll one card width; they are real controls.
- The filter is a real working control: All + the service groups (Create / Watch / Read / Keep), minimal JS, resets scroll to start.
- The whole card is the link; the Open pill is the same destination.
- Mobile: 34px header, 260px cards, 180px image boxes.

## Product marks (generative rule — run at build time, per service)
Best-effort waterfall, in order. Lock the winner into the page as a data-URI; never hotlink.
1. The service's own `apple-touch-icon.png` or largest favicon, fetched from the service URL.
2. The dashboard-icons public library: `https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/<slug>.png`.
3. Simple Icons (CC0 brand glyphs).
4. A designed default: single-color stroke SVG mark in ink or a brand-native color. This is also the legitimate answer for our own products (e.g. Inbox) — our services get our marks.
Monogram letters and emoji are banned. Marks are product shots, not decorations: centered, large, one per box.

## Voice
Taglines are generated per service from its fact, in Apple voice: short declarative fragments, period-terminated, second person implied, a little cutesy but literally true. Examples: Git → "Commit. Push. Ship." Sonarr → "Every episode, accounted for." Vaultwarden → "Every password, under lock and key." Bans: "revolutionary", "seamless", exclamation points, invented slogans, superlatives, all-caps. If a count appears, count the list.

## Motion
Slider scroll and arrow clicks: smooth-scroll, ~300ms. Filter switching is instant. Nothing loops, nothing autoplays.

## Anti-patterns (never do these)
- No viewport-filling hero header. The header is one line.
- No grids of equal boxes; the slider is the layout.
- No name/tagline/button inside the white image box.
- No monograms, emoji, or hotlinked images.
- No fake controls: filter, arrows, and Open must all work.
- No featured slots or invented hierarchy.
- No colors beyond the palette.

## Signature component
The product card: white 28px-radius image box with the service's mark centered → 28px name on gray → 17px tagline → blue Open pill. Repeated in a snap-scrolling slider under a working segmented filter. That card is a locked template; every service fills the same four slots.
