---
name: hotkey
display: Hotkey
mode: template
template: templates/hotkey.html
config: themes.config.json
inspiration: raycast.com
---

# Hotkey

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `build-themes` skill.

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

Marks and colors — an example palette from one intranet. Derive initials from the actual service names; reuse these colors, keeping neighbors distinct:

- Git G #F05033
- OpenCode V2 V2 #7B61FF
- OpenCode V1 V1 #4C78A8
- Vaultwarden V #175DDC
- Home Assistant H #18BCF2
- Inbox I #FF6363
- Sonarr S #35C5F4
- Radarr R #F7B731
- BookLore B #A55EEA
- NZBGet (Storage One) N #26DE81
- NZBGet (Storage Two) N2 #2BCBBA
- Dagu D #FD9644
- Storage One S1 #778CA3
- Storage Two S2 #4B6584

Default pins, in order: Git, OpenCode V2, OpenCode V1, Vaultwarden, Home Assistant, Inbox.

Facts stay true. Title is `Open` plus the service name.

## Do not

Do not redesign the bar. Do not add a Pin button to the search row. Do not use emoji. Do not add marketing sections. Do not say Raycast.
