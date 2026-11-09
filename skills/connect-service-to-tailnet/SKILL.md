---
name: connect-service-to-tailnet
description: Walk through making a service host or container reachable from the intranet gateway over Tailscale.
---

# connect-service-to-tailnet

Use inside `configure-service` when a service host or container is not already reachable from the gateway.

## Host connection paths

Ask where the service runs and whether its host is already a Tailscale node. Prefer direct tailnet membership over subnet routing. The supported paths in priority order:

1. **Direct tailnet binding**: The host is already a Tailscale node, and the service listens on all interfaces (`0.0.0.0`) or specifically on the host's Tailscale IP.
2. **Install Tailscale on host**: Install and join Tailscale on the service machine (Linux, macOS, Windows, Raspberry Pi), then expose the service port. Headless machines join with `tailscale up --auth-key=<key>` using a one-time key generated in the Tailscale admin console. Never store the key in `manifest.md` or the project repo, and disable key expiry for always-on servers in the admin console.
3. **Container integration**: For Docker or Unraid, use the platform's Tailscale integration or a Tailscale sidecar container so the service gets a stable, dedicated tailnet IP.
4. **Subnet routing**: Use a Tailscale subnet router only when direct host membership is not possible (e.g., closed IoT devices). Record the router and subnet in `manifest.md`. Never nest subnet routers.

## Verification

After configuring the path, verify reachability directly from the gateway:

```bash
curl -I http://<tailnet-ip-or-host>:<port><health-path>
```

Record the verified tailnet target (`<ip>:<port>`) and health check path in `manifest.md` under the service block.

## Access control

Tailscale's default policy lets every device on the tailnet reach every other device. That is fine for a single person's tailnet. If other people share it, tell the user the intranet is reachable by all of them, and offer to tighten the policy so only the gateway can reach service ports (for example, tag service hosts and allow `tag:gateway` → `tag:service:*`).
