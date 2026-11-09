---
name: hotkey
display: Hotkey
mode: template
template: templates/themes/hotkey.html
config: themes.config.json
inspiration: raycast.com
---

# Hotkey

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `configure-themes` skill.

## Config slots

- `pins` — up to 6 service names, the default pinned shortcuts (Control+1–6). User overrides persist in localStorage.
- `services[].name`, `url` — verbatim.
- `services[].fact` — the service fact verbatim.
- `services[].icon` — 1–2 character mark (initials).
- `services[].color` — one hex color per service, distinct from its neighbors.

Rows, filter, and keyboard nav render live from the config. Background choice is user state.

## Fill

Each service becomes one result row:

```html
<li><a href="URL" data-name="NAME">
  <span class="app-icon" style="background:COLOR">MARK</span>
  <span class="r-copy"><span class="r-title">Open NAME</span><br><span class="r-sub">FACT</span></span>
</a></li>
```

Use every service. First row also gets `class="hot"` and the first six default pins get `data-key` plus the two `kbd` chips.

Marks and colors — an example palette from one intranet. Derive initials from the actual service names; reuse these colors where the service matches, and pick new distinct colors for anything else, keeping neighbors distinct:

- Git G #F05033
- Nextcloud N #0082C9
- Paperless-ngx P #17541F
- Miniflux M #A55EEA
- Audiobookshelf A #C88A1E
- Jellyfin J #AA5CC3
- Sonarr S #35C5F4
- Radarr R #F7B731
- Immich I #FA2921
- Vaultwarden V #175DDC
- Home Assistant H #18BCF2
- Pi-hole Ph #96060C
- Uptime Kuma U #5CDD8B
- NAS S #778CA3

Default pins: up to six services the user says they open most; with no signal, the first six in manifest order. The sample config pins Git, Nextcloud, Vaultwarden, Home Assistant, Jellyfin, Immich.

Facts stay true. Title is `Open` plus the service name.

## Do not

Do not redesign the bar. Do not add a Pin button to the search row. Do not use emoji. Do not add marketing sections. Do not say Raycast.
