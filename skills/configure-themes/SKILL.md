---
name: configure-themes
description: Configure dashboard theme preferences and build the themed intranet pages. Use when the user asks to change the default theme, update pinned services, customize theme styling, or rebuild the dashboard suite.
---

# configure-themes

Configure and build the theme suite for the intranet dashboard. The kit ships seven locked themes. You author or update `themes.config.json`, then assemble pages mechanically from the skill's locked templates.

## The model

- **`manifest.md`** — the facts. Every `### Service` block gives `name`, `url`, `description`. Single source of truth. In `themes.config.json` and the templates, the manifest's `description` is called `fact`.
- **`themes.config.json`** (project root) — the presentation layer:
  - `themes` — enabled themes, ordered (the switcher roster).
  - `default` — the theme the landing page lands on initially.
  - `theme-options` — theme-level extras (Hotkey: `pins`; Bliss: `user`; Supply Co.: `company`, `branch`, `departments`; Intranet News: `title`).
  - `services[]` — core fields (`name`, `url`, `fact`) plus a namespace per enabled theme holding that theme's extras. Facts are refreshed from the manifest on every build; only namespaces are authored.
- **Locked templates** (located inside this skill at `templates/themes/<theme>.html`) — structure and behavior. Never edit to change copy.
- **Theme defs** (located inside this skill at `themes/<theme>.md`) — each theme's namespace slot rules and voice. Read the def before authoring its namespace.
- **Visitor state** — pins, icon positions, backgrounds, orders persist in localStorage in the browser.

## Operations

### 1. Rebuilding the theme suite
When services change or pages need to be regenerated:

1. If the project has no `themes.config.json` yet, create one: `themes` lists all seven (`hotkey`, `zinerack`, `infinite-loop`, `bliss`, `supply-co`, `intranet-news`, `network`), `default` is `hotkey`, and `theme-options` follows each theme def. The kit's `examples/themes.config.json` shows the shape, but its services are samples. Never copy them into a real dashboard.
2. Refresh core service fields in `themes.config.json` from `manifest.md`: `name` ← `name`, `url` ← `url`, `fact` ← `description`. Add a `services[]` entry for every manifest service, in manifest order, and author its namespaces per the theme defs. Drop entries whose service left the manifest. Every `url` must start with `http://` or `https://`; the templates turn anything else into a dead link.
3. For each enabled theme in `themes`:
   a. Copy the locked template `<skill_dir>/templates/themes/<theme>.html` → `build/<theme>.html` byte-exactly.
   b. Replace the contents of the `<script id="theme-config" type="application/json">` block with one JSON object: that theme's `theme-options` keys at the top level, plus `services` — each entry's core fields merged with its namespace for this theme (namespace keys win).
   c. Insert `<skill_dir>/templates/theme-settings.html` just before `</body>`, replacing the literal `CURRENT` with `<theme>.html` and `__ROSTER__` with the enabled themes as a JSON array `[["<theme>.html","<display>"],…]`. Each display name is the `display:` field in that theme's def.
4. Copy `<skill_dir>/templates/redirector.html` → `build/index.html`, replacing `__ROSTER__` with the same array and `__DEFAULT__` with `<default>.html`.
5. Verify built pages load and report their output location. Serving `build/` is the gateway's job (see `provision-gateway`).

### 2. Changing theme preferences
When the user asks to change default theme or customize options:

1. Update `default` or `theme-options` in `themes.config.json` (e.g., updating Hotkey pinned services or Supply Co. company name).
2. Re-run the assembly step above.
3. Confirm changes on the dashboard.

## Rules

- The manifest owns facts; `themes.config.json` owns presentation. Refresh facts every build.
- Never edit a built page directly. Update the config and rebuild.
- Never edit a locked template to customize a single user's intranet.
- Icon SVG (`bliss.svg`, `infinite-loop.art.svg`) should be simple shapes: `path`, `rect`, `circle`, `ellipse`, `line`, `polyline`, `polygon`. The templates strip anything else, including scripts and event handlers, so other markup just won't show.
