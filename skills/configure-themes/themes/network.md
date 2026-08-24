---
name: network
display: Network
inspiration: a spatial map of private infrastructure
mode: template
template: templates/network.html
config: themes.config.json
---

# Network

Config-driven theme. The template is locked; the only authored layer is the `network` namespace in `themes.config.json`, built per the `build-themes` skill. Never edit the template's CSS or structure to fix copy — edit the config.

## Config slots (the only authored layer)

- `services[].name`, `url` — verbatim from the services list.
- `services[].category` — one word, the node's coordinate label. Operational vocabulary: Forge, Agent, Queue, Home, Media, Books, Usenet, Vault, Jobs, Machine. Invent a new one only when none fits.
- `services[].meta` — one line, factual, no adjectives. Rewrite the service fact in this theme's terse voice ("Gitea forge — private git repositories" → "Gitea repositories").

Everything else — coordinates, counts, eyebrow, footer — renders live from the config. Never write a count anywhere.

## Vibe
A calm operations console for a private network. Cool teal light on deep blue-black, a faint blueprint grid, every node glowing softly. It feels like the room where the machines live — quiet, dark, everything green-lit and accounted for.

## Palette
- void: #07141D — page background
- panel: #091A24 — node surface
- panel-hot: #0D2833 — node hover
- ink: #DCEBF2 — primary text
- muted: #7FA2AE — secondary text
- dim: #527481 — labels and coordinates
- signal: #54D6D0 — status glow, the only saturated color
- hairline: rgba(152,211,220,.25) — structural lines

## Type
- Stack: "Avenir Next", "Segoe UI", sans-serif for names; ui-monospace for labels and coordinates.
- Display: clamp(35px,7vw,78px), weight 500, tracking -0.065em, line-height .92.
- Node names 15px semibold; metadata 10px monospace uppercase, letter-spaced .12em.
- Eyebrow labels: 11px monospace, uppercase, .18em tracking, signal color.

## Geometry
- Radius: 0. The grid is the geometry.
- Nodes are separated by 1px hairline gaps, not borders — a field of panels with void showing through.
- Status: an 8px circle of signal color with a soft glow, top-right of each node.
- No shadows except a single soft lift on hover (translateY -3px + soft black drop).

## Layout grammar
- Full-width header: huge display word left, a short factual note right, hairline beneath.
- Nodes in a strict 4-across grid, equal height, generous inner padding.
- Each node: monospace coordinate label (the port/category) top-left, status dot top-right, name at the bottom, one line of metadata beneath.
- Footer: two monospace facts, space-between, uppercase.
- Whitespace is structural and cool. Nothing centers.

## Voice
- Operational and terse. "Services and machines available inside the tailnet."
- Metadata is factual: "Gitea repositories", "Scheduled jobs". No adjectives.
- Banned: warmth, humor, exclamation, first person.

## Motion
- Hover lift only, 200ms ease. The status dots may pulse very slowly (3s+).
- Nothing else moves. The network is stable.

## Anti-patterns (never do these)
- No warm colors, no second accent hue.
- No rounded corners, no pills, no cards-with-borders.
- No illustrations, icons, or imagery beyond the status dot.
- No marketing copy, no hero theater beyond the one display word.
- No dense tables — this is a map, not a spreadsheet.
- No light mode.
- No invented telemetry. No alert counts, uptime, latency, or load figures. The only status claim is the dot. Footer facts must be literally true: the node count, that the tailnet is private.

## Signature component
The node: a square-ish panel of #091A24 separated from its neighbors by a 1px void gap, a glowing 8px teal dot top-right, a dim monospace coordinate top-left, and the service name in 15px semibold at the bottom. On hover the panel warms one step and lifts 3px.
