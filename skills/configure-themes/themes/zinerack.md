---
name: zinerack
display: Zinerack
mode: template
template: templates/zinerack.html
inspiration: gumroad.com
---

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `build-themes` skill.

## Config slots

- `services[].name`, `url` — verbatim.
- `services[].name` — core name, unless this theme renames the service (e.g. "NZBGet (Storage One)" → "NZBGet — Storage One"); only then carry a namespace `name`.
- `services[].pill` — one-word category tag (Code, Agents, House, TV, Film, Books, Keys, Machines).
- `services[].ds` — one line, first person, plain and a little wry. True to the service fact.

The headline count renders live (spelled out up to twenty). The `Est. 2026` tag is fixed — the year the site first went up.

# Zinerack

Template-based. Copy `templates/zinerack.html` to `site/gumroad.html`. Fill the card grid from the service list. Do not change the CSS or chrome.

## Fill

One card per service:

```html
<a class="card" target="_blank" rel="noopener noreferrer" href="URL"><span class="pill">TAG</span><span class="nm">NAME</span><span class="ds">LINE</span></a>
```

LINE is the fact, rewritten in short first person. TAG is one of: Code, Agents, House, TV, Film, Books, Keys, Machines.

The headline count is live. Do not type Fourteen or 14 into the heading. The page counts `.card` elements. If you add a service, add a card. The heading follows.

Tag at the top is `Est. YEAR`. YEAR is the year this intranet was first stood up. On this site that is 2026. Do not change it later when you add services. Do not write “est. at home.”

## Do not

Do not say Gumroad. Do not hardcode a count.
