# intranet-kit

intranet-kit is an opinionated package and process for building your personal intranet.

A personal intranet is a single place to reach every service you run, by name, from any of your devices. It is private and secure by default.

## What you get

- **A memorable name for every service.** `git.yourdomain.org`, `files.yourdomain.org`, `ai.yourdomain.org`. Every service gets a subdomain under one domain. You no longer need to remember an IP address, a port number, or a random URL.
- **Accessible everywhere.** The same URL works at home, on cellular, at a coffee shop, on your phone. Your services are reachable the same way regardless of where you are or what network you are on.
- **Private and secure by default.** Your services are visible only to devices you authorize. We recommend Tailscale for this: it creates a private network between your devices and your services without opening ports or managing firewall rules. This means you can even run services that have no authentication of their own — only your devices can reach them. See [Security](#security).
- **One place to find everything.** A dashboard at your root domain lists every service you run. If you forget how to access something, you come here and click. You can pull in public services you use alongside your own and curate the catalog.

## How it works

Three pieces work together.

### The gateway

The gateway is an entry point: an always-on machine that receives every request and sends it to the right service. It handles TLS and proxying so your services stay simple. It can run on an existing always-on machine or a small VPS.

### The tailnet

The tailnet is a private network built with Tailscale. It connects your gateway, the machines your services run on, and your own devices. When your laptop is on the tailnet, it can reach `git.yourdomain.org` as if it were on the same local network — even if the gateway is a VPS in another city.

### The dashboard

The dashboard is an easy way to access every service in your catalog. It lives at your root domain and lists each entry with a name, description, and link. The kit ships with several pre-built themes.

## Why these choices

intranet-kit is opinionated. These are the defaults, and they are the defaults for a reason.

- **Tailscale** keeps your services private without the complexity of VPN configuration or firewall management. It also gives every machine a stable address on the tailnet, which makes routing predictable.
- **One domain with subdomains** gives every service a memorable, predictable address. You never guess which port a service is on or which machine it runs on.
- **A gateway** centralizes routing and TLS. One machine handles certificates and proxying; your services stay simple and do not need their own public-facing configuration.

You can steer away from these defaults, but you void the warranty. Haha — jokes on you, there is no warranty. The kit is designed to work best when you stay on the rails.

## Security

The kit's whole security story is one move: **the gateway only listens on its tailnet address.** Everything else follows from it.

### How exposure works

Your domain's subdomains resolve publicly — anyone can look up what names exist. But resolving a name is not reaching a service. Every Caddy site block binds to the gateway's tailnet IP (for example, `100.x.y.z`), so the public internet gets silence and your tailnet devices get the service. This is why services with no login of their own are still safe to publish on your domain.

Two things to know:

- **Subdomain names are public.** Certificate transparency logs record every hostname that gets a TLS certificate. Anyone can enumerate your service names. Never put secrets in a subdomain name.
- **The gateway VPS is public.** It has an open SSH port by necessity. It holds no data — no databases, no files beyond static dashboard pages — so there is nothing to steal if it falls. Treat it as disposable: rebuildable from the manifest.

### Rules the kit follows

1. **Tailnet-only by default.** Gateway sites bind to the tailnet IP. A managed service is never routed from the public internet without an explicit user decision recorded in the manifest (`access: public`).
2. **Verify from outside.** After provisioning or any route change, confirm each service answers on the tailnet and refuses connections from the public internet. Both checks run every time.
3. **No secrets in the manifest.** The manifest describes topology — names, addresses, ports. It never contains passwords, API keys, or certificates.
4. **One entry point.** Services are reached through the gateway, never by opening ports on routers or machines. Tailscale handles reachability; the gateway handles routing.

If a service must be genuinely public — a blog, a public Gitea mirror — say so deliberately in the manifest and protect it with application-level authentication. The gateway will route it; the service must defend itself.

## The manifest

`manifest.md` is the source of truth for your intranet. It lives in your project and grows as you use the kit.

The manifest holds:

- **Your setup** — your domain, access method, gateway destination, and devices.
- **Your services** — the catalog of services in your intranet. Each entry has a name, URL, description, visibility, and routing information.

Skills read the manifest and keep it updated. For the full schema, see `manifest.md`.

## Vocabulary

Terms the skills use:

- **Manifest** — `manifest.md`, the file holding your configuration.
- **Domain path** — your naming choice: an owned domain or a pseudo-domain like `batcave.lan`.
- **Gateway** — the always-on machine that routes requests and serves the dashboard.
- **Tailnet** — your private Tailscale network.
- **Managed service** — a service you run on your own infrastructure.
- **Public external entry** — a service you do not run, such as a GitHub profile or an external tool.

## The skills

The kit includes a set of focused skills for setting up, managing, and maintaining your intranet.

| Skill | Lifecycle & Responsibility | User Triggers |
|-------|----------------------------|---------------|
| **configure-intranet** | **Top-level orchestrator**: First-time intake, domain choice, gateway location, initial cataloging, and coordinating the full build. | *"Set up my intranet"*, *"Reconfigure my intranet"*, *"Initialize my intranet"* |
| **configure-user-access** | **Device onboarding & access**: Getting the user's laptop/phone on Tailscale, auditing connected devices, and troubleshooting why a device can't reach the dashboard. | *"Add my phone to my intranet"*, *"I can't access my intranet from my laptop"*, *"Check my device access"* |
| **provision-gateway** | **Entry point & routing**: Installing Caddy + Tailscale on the gateway machine, managing certificates (DNS-01 ACME), re-rendering routes from the manifest, and verifying gateway health. | *"Set up the gateway"*, *"Reload gateway routes"*, *"Gateway is returning 502/down"* |
| **configure-service** | **Service lifecycle**: Adding a new service, updating ports/subdomains/names, removing a service, or troubleshooting a service that isn't responding. | *"Add Sonarr to my intranet"*, *"Change Gitea port to 3001"*, *"Remove old wiki"*, *"Fix my AI service link"* |
| **connect-service-to-tailnet** | **Host reachability plumbing**: Making the machine or container running a service reachable on the tailnet (host install, Docker sidecar, Unraid plugin, or subnet router). | *(Internal helper used by `configure-service` or called directly when a host isn't on Tailscale)* |
| **configure-themes** | **Presentation & dashboard**: Authoring/updating `themes.config.json` (pins, descriptions, default theme, styling) and assembling the static HTML pages. | *"Rebuild my landing page"*, *"Change default theme to Bliss"*, *"Update my pinned services"* |
