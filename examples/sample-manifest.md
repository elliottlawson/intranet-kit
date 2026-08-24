# Intranet manifest

Source of truth for this intranet. Grows as the kit is used. Edit this file; skills read it and keep it updated.

## Key

- `domain` — the single name everything hangs off. Real public domain (`example.org`) or pseudo-domain (`batcave.lan`).
- `access` — how it's reached: `tailnet` (recommended) or `public`.
- `host` — where the gateway runs: a cloud VPS, an always-on home box, or existing server.
- `gateway` — provisioning method: `host`, `docker`, or `vps`.
- `tailscale` — whether Tailscale is enabled (`yes` or `no`).
- `devices` — connected user devices.

## User

- domain: example.org
- access: tailnet
- host: vps
- gateway: host
- tailscale: yes
- devices: laptop, phone

## Services

One `###` block per service. Fields: `url`, `name`, `description`, `visibility`, `target`, `health`.

### Git
- url: https://git.example.org
- name: Git
- description: Gitea private git repositories
- visibility: tailnet
- target: 100.100.0.10:3000
- health: /

### Home Assistant
- url: https://home.example.org
- name: Home Assistant
- description: Home automation and smart devices
- visibility: tailnet
- target: 100.100.0.11:8123
- health: /

### GitHub
- url: https://github.com/example
- name: GitHub
- description: Public profile and repositories
- visibility: public
