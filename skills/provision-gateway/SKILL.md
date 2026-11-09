---
name: provision-gateway
description: Provision the intranet's Tailscale-connected gateway and its generated reverse-proxy configuration. Use when setting up the gateway, regenerating routes after a service change, or when the gateway is returning 502 or is down.
---

# provision-gateway

Use after `configure-intranet` has collected a destination and domain. The gateway is the stable entry point. It joins the tailnet, runs Caddy, serves the dashboard, and routes service subdomains to tailnet-reachable targets.

## Destination paths

Use the selected adapter. Keep the user-facing decisions the same across adapters:

- `host` installs Tailscale and Caddy on an always-on machine the user already has (a home server, NAS, or desktop that stays on).
- `vps` does the same on a small remote Linux VPS, plus host hardening (below). This is the recommended path: it keeps the front door off the home network.
- `docker` runs Caddy as a container on a user-managed Docker host. Use `network_mode: host` so Caddy can bind the host's tailnet IP, mount the same Caddyfile and env file, and use a Caddy image built with the DNS provider module.

Do not ask the user to rewrite the architecture for a different destination. The adapter owns installation details.

## Security requirements

The gateway's safety model is: names may be public, but only the tailnet can connect. Generated Caddy site blocks always `bind` to the gateway's tailnet IP so services are unreachable from the public internet. Never generate a route to a managed service that skips this.

- **Tailnet-only binding is the default.** Every site block binds to the tailnet IP. A managed service is served on a public interface only when its manifest block says `visibility: public` and the user confirmed it — see "Public services" below.
- **No secrets in generated files or the manifest.** The DNS provider token lives in an env file on the gateway (`/etc/caddy/env`, mode `600`, owned by root) and is referenced from the Caddyfile as `{env.DNS_API_TOKEN}`. Never write it into the Caddyfile, the manifest, or any file in the project repo. Scope the token to DNS edits on this one zone.
- **The gateway holds no data.** It can be rebuilt from the manifest at any time. Keep databases, files, and state on service hosts.
- **Admin API stays local.** Keep Caddy's admin endpoint on `127.0.0.1:2019`.
- **Host hardening for VPS destinations:** key-only SSH (password auth off), unattended security upgrades enabled, fail2ban or equivalent on SSH, no unused listening services. Verify each on first provision.

## DNS: how names reach the gateway

Pick one strategy per intranet and record it in the manifest's User section as `dns: public-records` or `dns: split`.

- **Public records (default for a real domain).** At the domain's DNS provider, create a wildcard `A` record `*.<domain>` → the gateway's **tailnet** IP (`100.x.y.z`), plus the same record for the bare `<domain>` if the dashboard lives there, DNS only (no provider proxying). Every tailnet device resolves every subdomain with no extra configuration; everyone else gets an address they cannot route to. The cost is that the gateway's tailnet IP is publicly visible, which reveals nothing usable.
- **Split DNS (required for a pseudo-domain like `batcave.lan`, optional otherwise).** Run CoreDNS on the gateway bound to its tailnet IP, answering `*.<domain>` with the tailnet IP. In the Tailscale admin console under DNS, add a split-DNS nameserver for `<domain>` pointing at the gateway's tailnet IP. Only tailnet devices can resolve the names.

Minimal Corefile for split DNS:

```
<domain>:53 {
    bind <gateway-tailnet-ip>
    template IN A {
        match "^(.+\.)?<domain-regex-escaped>\.$"
        answer "{{ .Name }} 300 IN A <gateway-tailnet-ip>"
    }
    template IN AAAA {
        rcode NOERROR
    }
    errors
}
```

Verify from a tailnet device: `dig +short git.<domain>` must return the gateway's tailnet IP.

## Certificates

- **Real domain:** Caddy gets certificates by the ACME DNS-01 challenge, which works even though nothing is publicly reachable. Caddy needs the module for the user's DNS provider compiled in (for example `xcaddy build --with github.com/caddy-dns/cloudflare`; other providers have their own `caddy-dns/*` module). If the provider has no module, delegate `_acme-challenge` with a CNAME to a zone on a supported provider.
- **Pseudo-domain:** no public CA will issue for it. Use `tls internal` and install Caddy's root certificate on each device, or tell the user plainly that browsers will warn.

## Caddyfile reference

Generate `/etc/caddy/Caddyfile` from the manifest: the gateway health block, the dashboard, and one site block per managed service with `visibility: tailnet`.

For a **real domain** (Caddy built with the DNS provider module):

```
{
	admin 127.0.0.1:2019
	acme_dns cloudflare {env.DNS_API_TOKEN}
}

# Health endpoint, reached by IP from inside the tailnet.
http://<gateway-tailnet-ip> {
	bind <gateway-tailnet-ip>
	respond "gateway OK"
}

# Dashboard: the build/ output of configure-themes.
<domain>, home.<domain> {
	bind <gateway-tailnet-ip>
	tls {
		resolvers 1.1.1.1
	}
	root * /srv/intranet
	file_server
}

git.<domain> {
	bind <gateway-tailnet-ip>
	tls {
		resolvers 1.1.1.1
	}
	reverse_proxy <service-target>
}
```

`resolvers 1.1.1.1` makes Caddy check its DNS-01 record against a public resolver, which avoids failures when the gateway itself uses split DNS. It only works alongside `acme_dns`.

For a **pseudo-domain**, use `local_certs` and no `tls` blocks at all. Caddy issues every site from its own local CA:

```
{
	admin 127.0.0.1:2019
	local_certs
}

http://<gateway-tailnet-ip> {
	bind <gateway-tailnet-ip>
	respond "gateway OK"
}

<domain>, home.<domain> {
	bind <gateway-tailnet-ip>
	root * /srv/intranet
	file_server
}

git.<domain> {
	bind <gateway-tailnet-ip>
	reverse_proxy <service-target>
}
```

The stock Caddy package has no DNS provider modules. With `acme_dns` it fails at startup with `module not registered: dns.providers.cloudflare`. Build a Caddy binary that includes the module (`xcaddy build --with github.com/caddy-dns/cloudflare`, or the download page at caddyserver.com/download with the module ticked) and install it in place of the packaged binary.

Run Caddy under systemd with `EnvironmentFile=/etc/caddy/env` and `After=tailscaled.service`. Copy the dashboard with `rsync -a --delete build/ <gateway>:/srv/intranet/` after every `configure-themes` build.

## Public services

A managed service with `visibility: public` is deliberately reachable from the internet. Before generating it, confirm with the user and confirm the service has its own login. Then:

- Give it a site block bound to the gateway's **public** IP (`bind <gateway-public-ip>`), not the tailnet IP.
- Point its DNS at the public IP with a specific `A` record (`blog.<domain>` → public IP); a specific record overrides the wildcard.
- Open only 80 and 443 for it; everything else stays tailnet-only.

Public external entries (links like a GitHub profile) need no gateway route at all.

## Steps

1. Validate the User section of `manifest.md` (domain, access, host, gateway, dns).
2. Ensure the gateway host is a Tailscale node and note its tailnet IP (`tailscale ip -4`). For a headless host, join with a one-time, pre-authorized auth key from the Tailscale admin console, tagged (for example `tag:gateway`) so it does not expire with a user's session.
3. Install Caddy with the DNS module for the selected adapter (and CoreDNS when `dns: split`). Write `/etc/caddy/env` with the token.
4. Generate the Caddyfile from the manifest as above. Validate it with `caddy validate --config /etc/caddy/Caddyfile` before installing, keep the previous file as a backup, then reload with `systemctl reload caddy`.
5. Set up DNS per the chosen strategy.
6. Copy the dashboard build to `/srv/intranet`.
7. Verify the health endpoint (`curl http://<gateway-tailnet-ip>/` from a tailnet device) and every registered service by name.
8. Run both exposure checks below. Record the date and result under a `## Gateway` section in `manifest.md`.
9. Report the gateway address, unresolved DNS or certificate work, and the next available action.

Provisioning is safe to rerun. Generated files are replaced from the manifest; they are never hand-edited as the source of truth.

## Exposure verification

Both checks run after provisioning and after any route change:

```bash
# 1. From a tailnet device: every service answers by name.
#    Add -k for a pseudo-domain, whose certificates come from the local CA.
curl -sI --max-time 8 https://<subdomain>.<domain> | head -1

# 2. From a machine NOT on the tailnet: tailnet services must not answer
#    on the public IP, even when asked for by name.
curl -skI --max-time 8 --resolve <subdomain>.<domain>:443:<gateway-public-ip> \
  https://<subdomain>.<domain> >/dev/null && echo "EXPOSED - stop" || echo "refused (good)"
nc -z -w 5 <gateway-public-ip> 53 && echo "EXPOSED - stop" || echo "refused (good)"
```

Check 2 must print `refused (good)` for every `visibility: tailnet` service and for port 53. Only `visibility: public` services may answer. If anything else answers, stop and fix the binding before reporting success. When the gateway is a home machine with no public IP of its own, run check 2 against the home network's public IP to confirm no router port forwards exist.

## Troubleshooting

- **Gateway health fails:** `systemctl status caddy`, then `journalctl -u caddy -n 50`. A bind error usually means Tailscale was not up when Caddy started; add `After=tailscaled.service` to the unit and restart.
- **Certificate errors:** check the journal for ACME messages. Common causes are a token without DNS edit rights on the zone, a missing DNS module, or a stale `_acme-challenge` record.
- **502 on one service:** the route works but the target does not answer. Hand off to `configure-service` troubleshooting.
- **Name does not resolve:** re-check the DNS strategy above with `dig` from a tailnet device.

## Teardown

To dismantle the intranet: delete the wildcard and any specific DNS records, revoke the DNS API token, stop and disable Caddy (and CoreDNS), remove the gateway from the tailnet in the admin console, and destroy the VPS if one was created. Leave `manifest.md` in place so the intranet can be rebuilt.
