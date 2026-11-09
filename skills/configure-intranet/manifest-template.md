# Intranet manifest (template)

Copy this file to the user's project as `manifest.md` and fill it in. It is the source of truth for an intranet built by the kit. This file grows as you use the kit — sections materialize when a skill first needs them. You (or the agent driving the kit) edit this file; skills read from it and keep it updated.

## Key

- `domain` — the single name everything hangs off. A real public domain (`example.org`) or a pseudo-domain (`batcave.lan`).
- `access` — how this intranet is reached. `tailnet` (recommended) or `public`.
- `host` — where the front door runs: a VPS, an always-on box, or existing infra.
- `gateway` — how the gateway is provisioned (`host`, `docker`, or `vps`).
- `dns` — how names reach the gateway: `public-records` (wildcard record at your DNS provider pointing at the gateway's tailnet IP) or `split` (resolved only inside the tailnet). Pseudo-domains always use `split`.
- `tailscale` — whether the private tailnet path is used and whether the user already has an account.
- `devices` — the user devices that should be able to reach the intranet.

## User

- domain: example.org
- access: tailnet
- host: vps
- gateway: vps
- dns: public-records
- tailscale: yes
- devices: laptop, phone

## Services

One `###` block per service. Fields:

- `url` — the full address the landing page links to.
- `name` — the human-readable name shown on the landing page.
- `description` — one line, shown under the name.
- `visibility` — `public` (reachable over the public internet) or `tailnet` (tailnet-only).
- `target` — the address the gateway reaches after tailnet setup. Omit for public external entries.
- `health` — an HTTP path used to verify the service. Omit for public external entries.

For **public external entries** (services you don't run, like a GitHub profile), only `url`, `name`, `description`, and `visibility: public` are needed. The `target` and `health` fields are for managed services only.

The block below shows the format only. It is not a service; don't copy it into a user's manifest unless they actually run Git.

```md
### Git
- url: https://git.example.org
- name: Git
- description: Gitea repositories
- visibility: tailnet
- target: 100.100.0.10:3000
- health: /
```

> The listing order is everything. The landing page renders services in their order here.
