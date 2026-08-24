# intranet-kit

An opinionated package and process for building your personal intranet.

A personal intranet is a single place to reach every service you run, by name, from any of your devices. It is private and secure by default.

---

## Quick start

Install the skill suite into your agent environment (OpenCode, Claude, Cursor, etc.):

```bash
npx skills add elliottlawson/intranet-kit
```

Then tell your agent:

```
Set up my intranet.
```

The agent will interview you about your services, domain preference, and gateway host, write your `manifest.md`, configure Caddy routing, and compile your 7-theme dashboard suite.

---

## What you get

- **A memorable name for every service.** `git.yourdomain.org`, `files.yourdomain.org`, `ai.yourdomain.org`. Every service gets a subdomain under one domain. You no longer need to remember an IP address, a port number, or a random URL.
- **Accessible everywhere.** The same URL works at home, on cellular, at a coffee shop, on your phone. Your services are reachable the same way regardless of where you are or what network you are on.
- **Private and secure by default.** Your services are visible only to devices you authorize. Tailscale creates a private encrypted mesh network between your devices and services without opening ports or managing complex firewall rules. Even services without built-in authentication stay private. See [Security](#security).
- **One place to find everything.** A static dashboard at your root domain lists every service you run, complete with 7 interchangeable visual themes.

---

## How it works

Three pieces work together:

1. **The gateway**: An always-on machine (home server or small VPS) that receives incoming traffic, handles automatic TLS certificates via DNS-01, and reverse-proxies subdomains to the correct services.
2. **The tailnet**: A private mesh network built with Tailscale. It connects your gateway, the machines your services run on, and your client devices.
3. **The dashboard**: A static dashboard generated from `manifest.md` and `themes.config.json` serving 7 distinct themes (Hotkey, Bliss, Supply Co, Infinite Loop, Zinerack, Network, Intranet News).

---

## Why these choices

intranet-kit is opinionated:

- **Tailscale** keeps your services private without VPN configuration or open firewall ports, giving every host a stable address.
- **One domain with subdomains** gives every service a clean, memorable address without guessing ports.
- **A gateway** centralizes routing and TLS certificates so individual services remain simple and unexposed.

---

## Security

The gateway only listens on its tailnet address. Public DNS resolves your subdomains, but only devices on your tailnet can reach them — services with no login of their own stay private. See [How it works](#how-it-works) or read the [security model](intranet.md#security).

---

## The skills

| Skill | Lifecycle & Responsibility | User Triggers |
|-------|----------------------------|---------------|
| **`configure-intranet`** | **Top-level orchestrator**: First-time intake, domain choice, gateway location, initial cataloging, and coordinating the full build. | *"Set up my intranet"*, *"Reconfigure my intranet"*, *"Initialize my intranet"* |
| **`configure-user-access`** | **Device onboarding & access**: Getting your laptop/phone on Tailscale, auditing connected devices, and diagnosing access issues. | *"Add my phone to my intranet"*, *"I can't access my intranet from my laptop"*, *"Check my device access"* |
| **`provision-gateway`** | **Entry point & routing**: Installing Caddy + Tailscale on the gateway, managing certificates (DNS-01 ACME), and re-rendering routes from the manifest. | *"Set up the gateway"*, *"Reload gateway routes"*, *"Gateway is returning 502/down"* |
| **`configure-service`** | **Service lifecycle**: Adding a new service, updating ports/subdomains/names, removing retired services, or troubleshooting unreachable services. | *"Add Sonarr to my intranet"*, *"Change Gitea port to 3001"*, *"Remove old wiki"*, *"Fix my AI service link"* |
| **`connect-service-to-tailnet`** | **Host reachability plumbing**: Making a service host or container reachable on the tailnet (direct binding, Docker integration, or subnet router). | *(Helper used by `configure-service` or called directly when a host needs tailnet access)* |
| **`configure-themes`** | **Presentation & dashboard**: Authoring/updating `themes.config.json` (pins, descriptions, default theme) and assembling the static HTML pages. | *"Rebuild my landing page"*, *"Change default theme to Bliss"*, *"Update my pinned services"* |

---

## The manifest

`manifest.md` is the durable source of truth for your intranet. It lives in your project root and records your operator settings, devices, and service catalog. Downstream skills read the manifest and keep it updated.

---

## License

[MIT License](LICENSE) © 2026 Elliott Lawson
