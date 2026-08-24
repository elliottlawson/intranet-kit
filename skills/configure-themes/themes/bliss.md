---
name: bliss
display: Bliss
mode: template
template: templates/bliss.html
config: themes.config.json
inspiration: microsoft.com/windows-xp
---

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `build-themes` skill.

## Config slots

- `services[].name`, `url` — verbatim.
- `services[].fact` — one short line, the service fact.
- `services[].size`, `use` — Add or Remove Programs texture ("18.2 MB", "used daily"). Playful but plausible; machines get TB and "always on".
- `services[].c1`, `c2` — the glossy icon tile gradient (light → dark).
- `services[].svg` — the icon glyph: simple 24×24 stroke paths, no fills.

Each service appears three ways from the same entry: desktop icon, Start menu program, Add/Remove Programs row. Icon positions, show-on-desktop toggles, and window state are user state in localStorage — never in config. Default layout is the auto grid.

# Windows XP (Luna)

## Vibe
Optimistic corporate futurism from 2001, rendered in glossy bevels. Candy-blue chrome over meadow-green fields — an OS that smiled at you. Every pixel is a control with a job to do; decoration exists only to teach you what is clickable.

## Palette
- chrome: #316AC5 — taskbar and window frame blue
- title bar: gradient #0058E6 → #3A93FF — active window title, horizontal gloss
- start: #3A913E — Start button green, glossy pill
- face: #ECE9D8 — dialog and button face, warm gray-beige
- bevel light: #FFFFFF / bevel mid: #ACA899 / bevel dark: #716F64 — the 3D edge triptych
- selection: #316AC5 — selected item fill, white text
- desktop: #004E98 — or Bliss wallpaper (green hill, saturated cumulus sky)
- ink: #000000 text on faces; #FFFFFF text on title bars
- danger: #D6544D — close button red with white ✕

## Type
- Stack: `Tahoma, "MS Sans Serif", sans-serif`, 11px base, everywhere. No other family.
- Title bars: 11px bold white Tahoma with a 1px dark text-shadow offset down-right.
- Start menu username band: larger white bold, slight shadow.
- Dialog text: 11px, black, sentence case, line-height 1.2.
- No type scale beyond 11px UI and ~13px window titles. No tracking changes. No serifs, ever.

## Geometry
- Window corners: 8px radius on TOP corners only; bottom corners square. Title bar carries the roundness.
- Bevels: 1px each of bevel-light (top/left) and bevel-mid/dark (bottom/right) around every button and field. Raised vs sunken is communicated purely by which edges are lit.
- Shadows: one soft drop shadow behind windows, ~8px blurred, gray. Never on buttons, never inside dialogs.
- Buttons: raised #ECE9D8 lozenge with full bevel set; pressed state inverts the bevels. 1px focus rect dot-pattern inside.

## Layout grammar
- Desktop first: Bliss wallpaper drawn as a smooth SVG landscape (curved hill paths, soft blurred clouds, subtle grass noise) — never flat polygon bands. The services live on it as PROGRAMS — each a 32px glossy icon with a label beneath, in a top-left column (or loose grid), like My Computer and Recycle Bin. Programs, never bare files.
- Every service is an installed program with its own drawn icon: a tiny glossy glyph in a rounded-square tile (blue document, green orb, orange gear — varied per program), label in 11px Tahoma with a desktop-label shadow. The glyph must be optically centered in the tile: flexbox centering, never absolute-inset guessing. The tile carries a top gloss highlight.
- Taskbar locked to bottom, 30px tall: Start pill left (real four-color wavy flag mark, not an orb), app task buttons, system tray right with a couple of tiny decorative icons and a live clock.
- The Start menu is the program launcher: green-blue two-column menu, user band on top, all programs listed with their icons, right column system places, footer with a live program count plus working Log Off and Turn Off Computer buttons. It must actually open and its items must actually link.
- The page boots straight to the desktop. No login gate, no boot screen, nothing between the visitor and the icons. The Welcome screen and shutdown sequence exist only as destinations reached from Start-menu actions, never by default.
- Log Off shows the Welcome screen (click user tile to return). Turn Off Computer opens the classic three-button dialog (Stand By / Turn Off / Restart) over a dimmed desktop, then "Bliss OS is shutting down…", then the safe-to-turn-off black screen with a power button that returns to the desktop.
- One optional open window on the desktop: "Add or Remove Programs" listing the services with icon, name, and size jokes in the details pane. It drags live by its title bar, minimizes into its taskbar button, and maximizes. Its chrome (menus, buttons) must either work or be plainly decorative.
- Add or Remove Programs doubles as the configuration surface: each row carries a "Desktop" checkbox controlling whether that service appears as a desktop icon (all on by default, persisted in localStorage). The Start menu always lists everything.
- Nothing is full-bleed; everything lives inside a framed, captioned box. Rubber-band marquee selection on empty desktop is welcome (decorative, unmistakably XP).

## Voice
Friendly machine. Imperative labels two words long ("Turn off computer", "My Recent Documents"). Explanations in one courteous sentence. Dialog buttons are verbs: OK / Cancel / Apply. Banned: enthusiasm, exclamation, emoji, emoji-as-icon. Example headline in-voice: "Welcome. Click your user name to continue."

## Motion
Small and snappy. Minimize/maximize zooms the window into its taskbar button, ~150ms. Menus slide or fade in under 150ms. Windows drag live with full contents. No easing curves beyond fast-out; no springs, no physics, no parallax. 300ms is a slow animation here.

## Anti-patterns (never do these)
- No flat design: every clickable element must show its bevels.
- No dark mode, no translucency, no background blur.
- No borderless buttons or text-link actions inside dialogs.
- No hamburger menus; use "File Edit View Help" menu bars.
- No rounded corners except the two 8px top corners of a window (and the Start pill).
- No giant display type; nothing over ~13px except the desktop wallpaper.
- No marketing hero layouts; the desktop is not a landing page.
- No animation over 300ms, no bounce, no inertia.
- No gradients besides the two sanctioned glosses (title bar blue, Start green).
- No pure-black #000 fills for chrome; chrome lives in the #ECE9D8→#716F64 bevel range or Luna blue.
- No official brand names in visible copy. The OS is "Bliss OS" — the words "Windows" and "Microsoft" never appear on screen.

## Signature component
The desktop program icon: a 32px glossy glyph tile (rounded 6px, top-light gradient, its own little symbol) above an 11px white Tahoma label with a soft dark shadow, sitting on the Bliss wallpaper. Double-click energy: the whole icon-plus-label is the link. Fourteen of them, each glyph distinct, arranged like someone actually uses this computer.
