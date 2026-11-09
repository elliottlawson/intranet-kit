# intranet-kit

intranet-kit is an opinionated way to build your own personal intranet with an AI agent.

A personal intranet is a single place to reach every service you run, by name, from any of your devices. It is private and secure by default.

## What you get

- **A memorable name for every service.** `git.yourdomain.org`, `files.yourdomain.org`, `photos.yourdomain.org`. Every service gets a subdomain under one domain. You no longer need to remember an IP address, a port number, or a random URL.
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

The dashboard is one page that links to every service in your catalog. It lives at your root domain and shows each entry with a name, a short description, and a link. The kit ships with seven themes.

## Why these choices

intranet-kit is opinionated. These are the defaults, and they are the defaults for a reason.

- **Tailscale** keeps your services private without the complexity of VPN configuration or firewall management. It also gives every machine a stable address on the tailnet, which makes routing predictable.
- **One domain with subdomains** gives every service a memorable, predictable address. You never guess which port a service is on or which machine it runs on.
- **A gateway** centralizes routing and TLS. One machine handles certificates and proxying; your services stay simple and do not need their own public-facing configuration.

You can steer away from these defaults, but you void the warranty. Haha — jokes on you, there is no warranty. The kit is designed to work best when you stay on the rails.

## Security

The kit's whole security story is one move: **the gateway only listens on its tailnet address.** Everything else follows from it.

### How exposure works

With a real domain, your service names are public: anyone can look up `git.yourdomain.org`. But looking up a name isn't the same as reaching the service. The name points at the gateway's tailnet address (something like `100.x.y.z`), and the gateway only listens on that address. Your tailnet devices get the service; the rest of the internet gets nothing. With a private name like `batcave.lan`, the names only resolve inside your tailnet in the first place. Either way, services with no login of their own stay private.

Two things to know:

- **Subdomain names are public.** Certificate transparency logs record every hostname that gets a TLS certificate. Anyone can enumerate your service names. Never put secrets in a subdomain name.
- **A VPS gateway is on the internet.** It needs an open SSH port so you can manage it. It holds no data beyond the static dashboard pages, so there's nothing to steal if it's compromised. Treat it as disposable: you can rebuild it from the manifest.

### Rules the kit follows

1. **Tailnet-only by default.** Gateway sites bind to the tailnet IP. A managed service is never routed from the public internet without an explicit user decision recorded in the manifest (`visibility: public` on that service).
2. **Check from both sides.** After setting up the gateway or changing a route, confirm each service answers from a tailnet device and refuses connections from the public internet. Both checks run every time.
3. **No secrets in the manifest.** The manifest describes topology — names, addresses, ports. It never contains passwords, API keys, or certificates.
4. **One entry point.** Services are reached through the gateway, never by opening ports on routers or machines. Tailscale handles reachability; the gateway handles routing.

If a service really needs to be public, like a blog or a public Gitea mirror, mark it public in the manifest on purpose and make sure it has its own login. The gateway will route it, but the service has to protect itself.

## The manifest

`manifest.md` is the source of truth for your intranet. It lives in your project and grows as you use the kit.

The manifest holds:

- **Your setup** — your domain, how names resolve, where the gateway runs, and your devices.
- **Your services** — the catalog of services in your intranet. Each entry has a name, URL, description, visibility, and routing information.

Skills read the manifest and keep it updated. For the full format, see `manifest-template.md` next to this file.

## Vocabulary

Terms the skills use:

- **Manifest** — `manifest.md`, the file holding your configuration.
- **Domain** — the name everything lives under: one you own, or a private name like `batcave.lan` (a pseudo-domain).
- **Gateway** — the always-on machine that routes requests and serves the dashboard.
- **Tailnet** — your private Tailscale network.
- **Managed service** — a service you run on your own infrastructure.
- **Public external entry** — a service you do not run, such as a GitHub profile or an external tool.

## The skills

The kit is a set of small skills. `configure-intranet` runs the first setup and calls the others in order; after that, each one handles a single kind of request.

| Skill | What it does | You might say |
|-------|--------------|---------------|
| **configure-intranet** | First-time setup: asks the questions, writes the manifest, and runs the other skills in order. | *"Set up my intranet"* |
| **configure-user-access** | Gets your laptop or phone onto Tailscale and figures out why a device can't connect. | *"Add my phone"*, *"I can't reach my intranet from my laptop"* |
| **provision-gateway** | Installs and configures the gateway: routes, certificates, DNS, and the exposure checks. | *"Set up the gateway"*, *"The gateway is down"* |
| **configure-service** | Adds, changes, removes, or fixes a single service. | *"Add Jellyfin"*, *"Gitea moved to port 3001"*, *"Remove the old wiki"* |
| **connect-service-to-tailnet** | Makes the machine a service runs on reachable over Tailscale. Usually called by `configure-service`. | *"My NAS isn't on Tailscale"* |
| **configure-themes** | Builds the dashboard and changes its themes, pins, and default look. | *"Rebuild my dashboard"*, *"Make Bliss the default"* |
