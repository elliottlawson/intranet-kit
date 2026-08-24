---
name: configure-themes
description: Configure dashboard theme preferences and build the themed intranet pages. Use when the user asks to change the default theme, update pinned services, customize theme styling, or rebuild the dashboard suite.
---

# configure-themes

Configure and build the theme suite for the intranet dashboard. The kit ships seven locked themes. You author or update `themes.config.json`, then assemble pages mechanically from the skill's locked templates.

## The model

- **`manifest.md`** — the facts. Every `### Service` block gives `name`, `url`, `description`. Single source of truth.
- **`themes.config.json`** (project root) — the presentation layer:
  - `themes` — enabled themes, ordered (the switcher roster).
  - `default` — the theme the landing page lands on initially.
  - `theme-options` — theme-level extras (Hotkey: `pins`; Supply Co.: `company`, `branch`, `departments`; Elliott News: `title`).
  - `services[]` — core fields (`name`, `url`, `description`) plus a namespace per enabled theme holding that theme's extras. Facts are refreshed from the manifest on every build; only namespaces are authored.
- **Locked templates** (located inside this skill at `templates/themes/<theme>.html`) — structure and behavior. Never edit to change copy.
- **Theme defs** (located inside this skill at `themes/<theme>.md`) — each theme's namespace slot rules and voice. Read the def before authoring its namespace.
- **Visitor state** — pins, icon positions, backgrounds, orders persist in localStorage in the browser.

## Operations

### 1. Rebuilding the theme suite
When services change or pages need to be regenerated:

1. Refresh core service fields (`name`, `url`, `description`) in `themes.config.json` from `manifest.md`.
2. For each enabled theme in `themes`:
   a. Copy the locked template `<skill_dir>/templates/themes/<theme>.html` → `build/<theme>.html` byte-exactly.
   b. Splice the `#theme-config` block: flatten the services array for that theme (core fields + theme namespace + theme-level options).
   c. Append `<skill_dir>/templates/theme-settings.html` before `</body>`; set `data-current` to the filename; insert the roster placeholder with enabled themes `[["<theme>.html","<display name>"],…]`.
3. Copy `<skill_dir>/templates/redirector.html` → `build/index.html`; fill the roster placeholder and set `__DEFAULT__` to `<default>.html`.
4. Verify built pages load and report their output location.

### 2. Changing theme preferences
When the user asks to change default theme or customize options:

1. Update `default` or `theme-options` in `themes.config.json` (e.g., updating Hotkey pinned services or Supply Co. company name).
2. Re-run the assembly step above.
3. Confirm changes on the dashboard.

## Rules

- The manifest owns facts; `themes.config.json` owns presentation. Refresh facts every build.
- Never edit a built page directly. Update the config and rebuild.
- Never edit a locked template to customize a single user's intranet.
