# intranet-kit

An opinionated way to build your own personal intranet with an AI agent.

A personal intranet is one place to reach every service you run, by name, from any of your devices. It's private by default: only your devices can get in.

---

## Quick start

Install the skills into your agent (OpenCode, Claude Code, Cursor, and others):

```bash
npx skills add elliottlawson/intranet-kit
```

The installer asks which agents to install for. Add `-a '*' -y` to install for all of them without prompting.

Then tell your agent:

```
Set up my intranet.
```

It will ask about your services, your domain, and where the gateway should run. Then it writes your `manifest.md`, sets up the gateway, and builds your dashboard.

### What you need first

- **A Tailscale account.** The free personal plan is enough. Install Tailscale on the devices you want to use.
- **A domain**, ideally at a DNS provider with an API (Cloudflare is the easiest). If you don't want to buy one, you can use a private name like `batcave.lan`. It only works on your own devices, and browsers will show certificate warnings unless you trust a local certificate.
- **A machine for the gateway**: a small Linux VPS (recommended) or a computer at home that stays on. You need SSH access to it.
- **Your services, already running.** The kit connects and names your services. It doesn't install them.

---

## What you get

- **A memorable name for every service.** `git.yourdomain.org`, `files.yourdomain.org`, `photos.yourdomain.org`. No more remembering IP addresses, port numbers, or random URLs.
- **The same address everywhere.** It works at home, on cellular, at a coffee shop, on your phone.
- **Private by default.** Only devices on your Tailscale network can reach your services, so even apps with no login of their own stay private. Nothing on your home network gets opened to the internet. See [Security](#security).
- **One page for everything.** A dashboard at your root domain links to every service you run, with seven themes to pick from.

---

## How it works

Three pieces work together:

1. **The gateway** is an always-on machine that receives every request and passes it to the right service. It also takes care of HTTPS certificates.
2. **The tailnet** is your private Tailscale network. It connects the gateway, the machines your services run on, and your own devices.
3. **The dashboard** is a set of static pages built from your `manifest.md`, in seven themes: Hotkey, Bliss, Supply Co., Infinite Loop, Zinerack, Network, and Intranet News.

`manifest.md` lives in your project and records your setup and your list of services. The skills read it and keep it up to date, so you can rebuild everything from it.

---

## Security

The gateway only listens on its Tailscale address. Your service names can be looked up publicly, but only devices on your tailnet can connect to them. The full model is in [intranet.md](skills/configure-intranet/intranet.md#security).

---

## The skills

You don't need to know these names. Just ask for what you want, and the agent picks the right one.

| Skill | What it does | You might say |
|-------|--------------|---------------|
| **`configure-intranet`** | First-time setup: asks the questions, writes the manifest, and runs the other skills in order. | *"Set up my intranet"* |
| **`configure-user-access`** | Gets your laptop or phone onto Tailscale and figures out why a device can't connect. | *"Add my phone"*, *"I can't reach my intranet from my laptop"* |
| **`provision-gateway`** | Installs and configures the gateway: routes, certificates, DNS, and the exposure checks. | *"Set up the gateway"*, *"The gateway is down"* |
| **`configure-service`** | Adds, changes, removes, or fixes a single service. | *"Add Jellyfin"*, *"Gitea moved to port 3001"*, *"Remove the old wiki"* |
| **`connect-service-to-tailnet`** | Makes the machine a service runs on reachable over Tailscale. Usually called by `configure-service`. | *"My NAS isn't on Tailscale"* |
| **`configure-themes`** | Builds the dashboard and changes its themes, pins, and default look. | *"Rebuild my dashboard"*, *"Make Bliss the default"* |

---

## License

[MIT](LICENSE) © 2026 Elliott Lawson
