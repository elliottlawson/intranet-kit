---
name: configure-service
description: Add, update, remove, or troubleshoot an individual service on the intranet. Use when the user asks to add a service, change a service port/subdomain, remove a service, or fix an unreachable service.
---

# configure-service

Manage the lifecycle of services on the intranet. Handles adding new services, updating configuration (ports, subdomains, visibility), removing decommissioned services, and troubleshooting service reachability.

## Operations

### 1. Adding a new service

1. **Identify the service**:
   - **Managed private service**: application running on user hardware.
   - **Public external entry**: public external link (e.g., GitHub, web tools).
2. **Collect details**:
   - For public links: name, URL, description, visibility (`public`).
   - For managed services: name, description, host machine/container, local port or upstream URL, desired subdomain, health check path (default `/`).
3. **Ensure host reachability**: Use `connect-service-to-tailnet` to make sure the host or container is reachable from the gateway.
4. **Update `manifest.md`**: write the `### <Name>` block under `## Services`.
5. **Update gateway routes**: reload gateway configuration with the new route.
6. **Rebuild dashboard**: run `configure-themes` to update the landing page.
7. **Verify**: test access to `https://<subdomain>.<domain>` and confirm HTTP 200/expected response.

### 2. Updating an existing service

When a port changes, machine moves, or subdomain is renamed:

1. Locate the service block in `manifest.md`.
2. Update the changed fields (target IP/port, subdomain, description, visibility).
3. Update gateway routes and reload Caddy.
4. If name/description changed, run `configure-themes` to refresh the dashboard.
5. Verify reachability at the new endpoint.

### 3. Removing a service

When a service is retired:

1. Remove the service block from `manifest.md`.
2. Remove the route from the gateway Caddyfile and reload Caddy.
3. Run `configure-themes` to remove the service from the dashboard.
4. Verify the subdomain no longer resolves/routes.

### 4. Troubleshooting a service

When a service returns 502 Bad Gateway, 404, or fails to load:

1. **Check local service**: Is the application running and listening on the expected port on its host?
2. **Check tailnet connectivity**: Can the gateway ping or curl the service host's tailnet IP and port?
3. **Check gateway configuration**: Does Caddy have the correct reverse_proxy target in its Caddyfile?
4. **Check health endpoint**: Does `curl -I http://<target><health_path>` return a valid response?
5. Fix the root cause, reload the gateway if needed, and verify from a client device.
