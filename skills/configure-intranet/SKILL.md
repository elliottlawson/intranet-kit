---
name: configure-intranet
description: Set up, configure, or update a personal intranet. Use when the user says things like "set up my intranet", "configure my intranet", "help me build my personal intranet", or asks to add services or modify an existing intranet.
---

# configure-intranet

## Purpose

Guide the user through establishing a new personal intranet or updating an existing one. Leaves the user with a configured `manifest.md`, a running gateway, and a functioning dashboard.

## Interview Principles

- **Use interactive tools when available**: If an interactive question or choice tool (e.g. `question`, `ask_user`) is available in the runtime, use it to present the foundational choices (Tailscale status, Domain type, Gateway host) as a clean interactive step.
- **One question at a time in chat**: If no interactive question tool is available, ask questions strictly one by one in chat. Never dump a multi-part questionnaire.
- **Record decisions progressively** in `manifest.md` as they are answered.

---

## Orientation

1. **Read `intranet.md`** to load the model and vocabulary.
2. **Check for `manifest.md`**:
   - **If present:** The user is updating an existing intranet. Acknowledge current setup, and ask what they want to add or modify.
   - **If absent:** This is a new intranet setup. Proceed to Stage 1.

---

## Stage 1: Foundational Architecture

Collect the 3 foundational architecture decisions:
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

Once the foundational architecture is established, output a single concise message in chat:

1. Ask for their specific domain name (e.g. `example.org` or `home.lan`).
2. Ask what services or links they want on their dashboard:
   *"What services or links would you like on your dashboard? (For example: Gitea, Home Assistant, Radarr, media servers, or public links like your GitHub)"*
3. Ask which devices they will use to access the intranet (e.g. laptop, phone).

When the user replies:
- Write the services into `manifest.md` under `## Services`.
- Write the devices into `manifest.md` under `## User` → `devices`.
- Record any hostnames, IPs, or ports provided. Do not grill the user for missing ports; unconfigured private services will have routes generated during provisioning.

---

## Stage 3: Confirmation Gate

Summarize the decisions in a concise message and confirm before building:

> *"Here is our configuration:
> - **Domain:** `<domain>`
> - **Gateway:** `<location>`
> - **Tailscale:** `<yes/setup-needed>`
> - **Devices:** `<devices>`
> - **Services:** `<list of services>`
>
> Ready to provision the gateway, wire the services, and build the dashboard?"*

---

## Stage 4: Execution Pipeline

On confirmation, execute in order. If any downstream skill is not preloaded in the current agent session, read its instructions directly from `.agents/skills/<name>/SKILL.md`:

1. **Write `manifest.md`** with all recorded choices.
2. **Connect user devices** via `configure-user-access` (`skills/configure-user-access/SKILL.md`) if Tailscale onboarding is needed.
3. **Provision the gateway** via `provision-gateway` (`skills/provision-gateway/SKILL.md`) on the chosen host.
4. **Wire services** via `configure-service` (`skills/configure-service/SKILL.md`) for private services to configure routing and health checks.
5. **Assemble dashboard** via `configure-themes` (`skills/configure-themes/SKILL.md`) to generate the 7-theme suite in `build/`.
6. **Verify & Report**: Confirm the dashboard responds and present the URL to the user.
