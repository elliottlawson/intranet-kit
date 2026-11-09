---
name: configure-intranet
description: Set up, configure, or update a personal intranet. Use when the user says things like "set up my intranet", "configure my intranet", "help me build my personal intranet", or asks to add services or modify an existing intranet.
---

# configure-intranet

## Purpose

Walk the user through setting up a new personal intranet, or changing an existing one. When you're done, the user has a filled-in `manifest.md`, a running gateway, and a working dashboard.

## How to interview

- **Use a question tool if you have one.** If your runtime has a multiple-choice tool (for example `question` or `ask_user`), use it for the three setup questions in Stage 1.
- **Otherwise, ask one question at a time.** Never send a long questionnaire in one message.
- **Write answers into `manifest.md` as you get them**, so nothing is lost if the conversation stops.

---

## Orientation

1. **Read `intranet.md` and `manifest-template.md`** in this skill's directory. The first explains the model and vocabulary; the second is the format of the manifest you'll write.
2. **Check for `manifest.md`**:
   - **If it exists:** the user is changing an existing intranet. Briefly summarize what's there and ask what they want to change.
   - **If it doesn't:** this is a new setup. Go to Stage 1.

---

## Stage 1: The three big choices

Ask these three questions first:
1. **Tailscale network status** (`Yes, using Tailscale` vs `Need Tailscale setup`).
2. **Domain type** (`Owned domain` vs `Private .lan`).
3. **Gateway host** (`Home server / always-on box` vs `Cloud VPS`).

**If an interactive question tool is available**, call it with these questions:

```json
{
  "questions": [
    {
      "header": "Tailscale network",
      "question": "Do you already have a Tailscale network set up?",
      "options": [
        { "label": "Yes, using Tailscale", "description": "My devices and/or services are already on a tailnet" },
        { "label": "Need Tailscale setup", "description": "I need help setting up a free Tailscale account" }
      ]
    },
    {
      "header": "Domain type",
      "question": "What kind of domain would you like to use for your intranet?",
      "options": [
        { "label": "Owned domain", "description": "e.g. yourdomain.org — standard and recommended" },
        { "label": "Private .lan", "description": "e.g. home.lan or batcave.lan — local/tailnet only" }
      ]
    },
    {
      "header": "Gateway host",
      "question": "Where will your always-on gateway run? (The machine that routes traffic and serves the dashboard)",
      "options": [
        { "label": "Home server / machine", "description": "Mac Mini, Linux server, Raspberry Pi, NAS" },
        { "label": "Cloud VPS", "description": "DigitalOcean, Hetzner, etc. — always on with stable DNS" }
      ]
    }
  ]
}
```

**If no question tool is available**, ask these three items one by one in chat.

Record the answers in `manifest.md` under `## User`.

---

## Stage 2: Services & Specifics (In Chat)

Once those are answered, send one short message in chat that asks for:

1. Their domain name (for example `example.org` or `home.lan`).
2. The services and links they want on the dashboard:
   *"What services or links would you like on your dashboard? For example: Gitea, Home Assistant, Jellyfin, or public links like your GitHub."*
3. The devices they'll use to reach the intranet (for example a laptop and a phone).

When the user replies:
- Write the services into `manifest.md` under `## Services`.
- Write the devices into `manifest.md` under `## User` → `devices`.
- Record any hostnames, IPs, or ports they mention. Don't press for missing details now; `configure-service` collects them when it wires each service.

---

## Stage 3: Confirm before building

Summarize the choices in one short message and wait for a yes:

> *"Here is our configuration:
> - **Domain:** `<domain>`
> - **Gateway:** `<location>`
> - **Tailscale:** `<yes/setup-needed>`
> - **Devices:** `<devices>`
> - **Services:** `<list of services>`
>
> Ready to provision the gateway, wire the services, and build the dashboard?"*

---

## Stage 4: Build it

Once the user confirms, run these in order. If any downstream skill is not preloaded in the current agent session, read its `SKILL.md` from wherever this skill suite is installed (the sibling directory `../<name>/SKILL.md` relative to this file; commonly `.agents/skills/<name>/`):

1. **Write `manifest.md`** in the user's project, following `manifest-template.md`, with everything recorded so far.
2. **Connect user devices** via `configure-user-access` if Tailscale onboarding is needed.
3. **Provision the gateway** via `provision-gateway` on the chosen host.
4. **Wire services** via `configure-service` for private services to configure routing and health checks.
5. **Assemble dashboard** via `configure-themes` to generate the 7-theme suite in `build/`, then copy `build/` to the gateway's dashboard root as described in `provision-gateway`.
6. **Check and report**: confirm the dashboard loads, then give the user its URL.
