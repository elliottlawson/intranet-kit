---
name: elliott-news
display: Intranet News
inspiration: news.ycombinator.com
mode: template
template: templates/elliott-news.html
config: themes.config.json
---

# Elliott News

Config-driven theme. The template is locked; the only authored layer is this theme's namespace in `themes.config.json`, built per the `build-themes` skill.

## Config slots

- `services[].name`, `url` — verbatim from the services list.
- `services[].fact` — one line, the service fact verbatim. No editorializing.

The masthead title comes from `theme-options.elliott-news.title` — derive it from the user's name or domain ("Demo News"); default is "Intranet News". Never ship another person's name.

Ranks, domains, and the footer count render live. Story order is user state (localStorage, keyed by url); "new" resets it. The nav items past/comments/ask/show/jobs/submit are inert texture — plain spans, never links.

## Design reference (historical — the look is locked in the template)

A dense front-page list of the services in `services.md`. It should look like the orange-bar bulletin board in the gold screenshot: one column on beige, numbered rows, title and domain on one line, fact indented under it.

## Anatomy
Beige #F6F6EF to the edges. A centered column, about 92% wide, max 1050px, with a little beige above the bar. The orange bar is only as wide as that column, not a full-viewport stripe with the list hanging off it.

Orange bar #FF6600, a few pixels of padding. Left: 13px bold white `Elliott News`. Beside it, 11px light orange text: `new | past | comments | ask | show | jobs | submit`. Those words are costume. They are spans, not links.

Then the numbered list. Then a 1px orange rule. Then `Fourteen services. Tailnet only.` in 11.5px #828282.

## Story row
One row per service, in `services.md` order. The row is a single visual unit:

`1. ▲ Git (git.example.org)`

The rank, a small gray triangle, the service name, and the host in parentheses sit on one line. The name is the service link, black 13.3px Verdana. Domain is #828282.

The triangle is a real control. Clicking it swaps that row with the one above and renumbers the list. The top row's triangle does nothing. Remember the order in localStorage. The costume word `new` is also real: it restores the `services.md` order. Other pipe words stay spans.

Directly under the title, indented to the name, one 11.5px #828282 line: the fact, terse and true. A second true clause is allowed if it is still a fact.

Leave about 10px of beige under that fact before the next rank. The list should feel like the gold screenshot: dense, but each story is its own block. Do not glue rows together.

Do not put the number on its own line. Do not let the title wrap under the number.

## Mobile
Same column, a bit wider. Bar still contains the wordmark. Costume nav may wrap or hide. Rows stay `n. ▲ name (host)` then an indented fact.

## Palette
paper #F6F6EF, header #FF6600, ink #000000, muted #828282, bar-nav #FFD9B8.

## Type
Verdana, Geneva, sans-serif only. Titles 13.3px. Metadata 11.5px. No headings.

## Voice
Labels and facts. Never say Hacker News.

## Motion
None beyond the browser underline on the service name.

## Anti-patterns
- No stacked rank above the title
- No full-bleed orange bar with a disconnected list
- No cards, rounded corners, shadows, or dark mode
- No working comments, submit, or upvote
- No dropped services
- No invented points or timestamps
