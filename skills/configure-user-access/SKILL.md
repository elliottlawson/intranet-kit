---
name: configure-user-access
description: Onboard user devices, audit connected devices, and troubleshoot access issues reaching the intranet. Use when the user asks to add a device, connect their phone/laptop to the intranet, or can't reach intranet services.
---

# configure-user-access

Manage the user's personal devices and access to the intranet. Handles onboarding new devices, auditing connected hardware, and diagnosing why a device cannot reach the intranet.

## Scenarios

### 1. Onboarding new devices
When the user wants to add a new device (laptop, phone, tablet):

1. Ask which device they want to add.
2. Check if Tailscale is installed on that device.
   - If not installed: guide them to install Tailscale from `tailscale.com/download` or the device's app store.
   - If installed: ask them to sign into their existing Tailscale network.
3. Verify connection: have the user open the intranet dashboard (`https://<domain>`) or ping the gateway from that device.
4. Update `manifest.md` under `## User` → `devices` with the newly connected device.

### 2. First-time account setup
When the user does not have a Tailscale account:

1. Explain that Tailscale provides secure encrypted access between their devices and services without opening firewall ports.
2. Guide them to sign up at `login.tailscale.com` (free personal tier).
3. Walk them through installing Tailscale on their primary device first.
4. Record the primary device in `manifest.md`.

### 3. Troubleshooting access issues
When a user says they cannot reach their intranet or a specific service from a device:

1. **Verify Tailscale status**: Is the Tailscale client active/connected on that device?
2. **Verify gateway reachability**: Can the device ping or reach the gateway's tailnet IP?
3. **Verify DNS resolution**: Is the requested subdomain (e.g., `git.yourdomain.org`) resolving to the gateway's tailnet IP?
4. **Verify certificates**: Is the browser showing an SSL warning, or is the connection refused?
5. Fix the identified issue (reconnecting VPN, setting MagicDNS / split DNS, or fixing gateway TLS) and confirm access.
