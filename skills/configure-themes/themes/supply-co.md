---
name: supply-co
display: Supply Co.
inspiration: mcmaster.com
mode: template
template: templates/supply-co.html
config: themes.config.json
---

# Supply Co.

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `build-themes` skill.

## Config slots

- `company`, `branch` — the storefront's name and branch line ("Acme Supply Co.", "Tailnet Branch"). Use the domain ("<Domain> Supply Co.") unless the manifest records a person or family name.
- `departments[]` — `id` + `name`, ordered. Group the services into 3–6 plain-category departments.
- `services[].name`, `url` — verbatim.
- `services[].sku` — part number: `EL-` + 2–4 char uppercase code derived from the service name.
- `services[].fact` — the service fact in Title Case.
- `services[].department` — a department `id`.

All counts ("N products", "Stock: N") and singular/plural render live. Empty departments are omitted.

## Intent
Reproduce the Acme Supply Co. catalog: a paper-white industrial spec sheet. The page is a directory of stocked services, not a marketing site and not a replica of McMaster.com's search store.

## First viewport
White page. A short white masthead with a 2px #FED700 rule under it. Green store name on the left. Small gray status text on the right, separated by pipes, not a filled green bar. Below that: a 190px department sidebar on the left and the catalog on the right. No search. No cart. No account. No emoji.

## Masthead
Height is content, about 10px 16px padding. Left: `<company>` in 16px bold #336633, then `— Tailnet Branch` in regular #333. Right: 11px #666 text: `Catalog | Order Status: All Systems Stocked | Branch: Home`. The yellow rule is the only accent. Do not put the stock sentence in the masthead.

## Sidebar
190px, 1px #CCC on the right. Heading `DEPARTMENTS` in 11px bold uppercase #666. Links are 12px #333, not blue. Selected link is solid #FFE600 and bold. Hover is #FFFFB5. Each link scrolls to its section.

## Main column
Breadcrumb in 11px #666: `Catalog > All Services > Tailnet Branch`. Title `Intranet Services` at 19px bold #336633. One blurb under it: `14 services in stock. Available same session. Tailnet delivery only.` Count must match `services.md`. Do not repeat that sentence.

## Departments
Use every service.

- Agents & Automation: OpenCode V2, OpenCode V1, Inbox, Dagu
- Media Management: Sonarr, Radarr, BookLore, NZBGet (Storage One), NZBGet (Storage Two)
- Infrastructure: Git, Vaultwarden, Storage One, Storage Two
- Home: Home Assistant

Each section has a 19px green title with a 1px #CCC rule above it, then an 11px #999 line `N products` or `1 product`, then the grid.

## Product cell
Compact spec cards in a wrapping grid, `repeat(auto-fill, minmax(160px, 1fr))`, 8px gap. The cell is a hairline #EEE box with 8px 10px padding. A one-item row stays 160–220px wide. Never let a lone cell grow to the full main column. The cell is the link.

Inside, three lines only:
- Part number, 11px monospace #999, scheme `EL-OC2`, `EL-OC1`, `EL-INB`, `EL-DAG`, `EL-SON`, `EL-RAD`, `EL-BKL`, `EL-NZ1`, `EL-NZ2`, `EL-GIT`, `EL-VLT`, `EL-SH0`, `EL-SU0`, `EL-HAS`
- Service name, 12px bold #006699
- Noun-phrase fact, 10.5px #666, title case, no second person

No thumbnails. No emoji. No icons.

## Footer
A 1px #CCC rule. 10px uppercase #999. Left: `LAWSON SUPPLY CO. — TAILNET BRANCH`. Right: `PRICES: NONE. STOCK: 14.`

## Mobile
Drop the masthead status line. Stack departments as a horizontal row of plain 12px text links, same as the desktop sidebar, no boxes, no pills, no borders, no button chrome. One column of cells. Do not keep a 190px sidebar beside the catalog.

## Palette
paper #FFFFFF, ink #333333, header #336633, accent #FED700, highlight #FFE600, hover #FFFFB5, link #006699, line #CCCCCC, muted #666 / #999.

## Type
12px Arial, Helvetica, sans-serif. Nothing larger than 19px. No display type. No pills.

## Voice
Catalog nouns. Never McMaster. Never guaranteed. Never a wrong count.

## Anti-patterns
- No green filled masthead
- No search, Account, Cart, Help
- No emoji or fake photos
- No stock line in two places
- No full-width leftover cells
- No boxed or pill department links on mobile
- No blue sidebar links
- No dropped services
- No shadows, glass, or dark mode
