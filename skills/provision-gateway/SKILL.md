---
name: provision-gateway
description: Provision the intranet's Tailscale-connected gateway and its generated reverse-proxy configuration.
---

# provision-gateway

Use after `configure-intranet` has collected a destination and domain. The gateway is the stable entry point. It joins the tailnet, runs Caddy, and routes service subdomains to tailnet-reachable targets.

## Destination paths

Use the selected adapter. Keep the user-facing decisions the same across adapters:

- `host` installs Tailscale and Caddy on an always-on machine.
- `docker` installs the gateway container on a user-managed Docker host.
- `vps` installs Tailscale, Caddy, and private DNS on a remote VPS.

Do not ask the user to rewrite the architecture for a different destination. The adapter owns installation details.

## Security requirements

The gateway's safety model is: public DNS resolves, the tailnet delivers. Generated Caddy site blocks always `bind` to the gateway's tailnet IP so services are unreachable from the public internet. Never generate a route to a managed service that skips this.

- **Tailnet-only binding is the default.** Every site block binds to the tailnet IP. Routing a managed service from a public interface requires an explicit user decision recorded as `access: public` in the manifest — confirm before building it.
- **No secrets in generated files or the manifest.** TLS uses DNS-01 (Cloudflare token lives only in the gateway service config). The manifest holds names and addresses, never credentials.
- **The gateway holds no data.** It can be rebuilt from the manifest at any time. Keep databases, files, and state on service hosts.
- **Host hardening for VPS destinations:** key-only SSH (password auth off), unattended security upgrades enabled, no unused listening services. Verify each on first provision.

## Steps

1. Validate the user section of `manifest.md`.
2. Ensure the gateway host is or will be a Tailscale node.
3. Install or render the gateway package for the selected adapter.
4. Generate Caddy routes from registered services. Bind every route to the tailnet IP.
5. Configure the domain's private DNS path when the adapter supports it.
6. Reload the gateway.
7. Verify the gateway health endpoint and every registered service.
8. Run both exposure checks below and record the results.
9. Report the gateway address, unresolved DNS or certificate work, and the next available action.

Provisioning is safe to rerun. Generated files are replaced from the manifest; they are never hand-edited as the source of truth.

## Exposure verification

Both checks run after provisioning and after any route change:

```bash
# 1. From a tailnet device — every service answers.
curl -sI --max-time 8 https://<subdomain>.<domain> | head -1

# 2. From outside the tailnet — every port refuses.
#    Public IP should reject HTTP, HTTPS, and DNS unless access: public was chosen.
nc -vz -G 5 <gateway-public-ip> 80 443 53
```

If check 2 succeeds where it should fail, stop and fix the binding before reporting success. A service reachable from the public internet without `access: public` is a misconfiguration, not a feature.
