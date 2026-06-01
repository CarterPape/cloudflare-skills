---
name: cloudflare-one
description: "Guides Cloudflare One Zero Trust and SASE work across Access, Gateway, WARP, Tunnel, Magic WAN, DLP, CASB, device posture, and identity. Use when designing, configuring, troubleshooting, or reviewing Cloudflare One deployments. Retrieval-first: use current Cloudflare docs/API schemas instead of embedded product docs."
---

# Cloudflare One

Do not use this skill as product documentation. Before citing limits, settings, API fields, category IDs, or exact UI paths, retrieve current information from `developers.cloudflare.com/cloudflare-one/`, the Cloudflare docs MCP server, or the Cloudflare API schema.

## Workflow

1. Classify the ask: architecture, configuration, troubleshooting, migration, or review.
2. Gather context: account ID, users/sites/apps, identity provider, SCIM/group sync, device management, traffic path, compliance constraints, and rollout blast radius.
3. Retrieve only the current docs needed for the products involved: Access, Gateway, WARP, Tunnel, Magic WAN, DLP, CASB, device posture, or identity.
4. If account access is available, inspect existing resources before proposing or making changes: Access apps/policies/groups/IdPs, Gateway rules/lists/categories, device profiles/posture checks, tunnels/routes, DNS/resolver settings, and locations/sites.
5. Propose the change set with prerequisites, validation, and rollback. For risky changes, stage disabled or scoped to a pilot group/site unless the user explicitly asks otherwise.

## High-Value Guardrails

- Access controls application authorization; Gateway controls traffic inspection/filtering. Use both when the requirement spans identity-aware app access and network/web security.
- Public hostname Access apps can be clientless. Private destination apps require WARP or another network on-ramp plus routes and DNS resolution.
- Cloudflare Tunnel is an off-ramp from a private network to Cloudflare. Magic WAN/site connectivity is not a drop-in replacement for per-user application access.
- Group-based policies depend on IdP group claims or SCIM. If group sync is missing, do not invent group selectors.
- Private hostnames need explicit DNS routing/resolution; creating an Access app alone is not enough.
- HTTP inspection and DLP for encrypted web traffic require TLS inspection and planned Do Not Inspect exceptions.
- Gateway DNS, Network, HTTP, and Egress policies have different evaluation semantics. Retrieve current docs before explaining precedence.
- Start broad block/allow/DLP/TLS policies disabled, in audit, or limited to a pilot unless the user approves a wider rollout.

## Non-Obvious Product Context

### Identity and Access

- Access Groups are Cloudflare objects; IdP/SCIM groups are identity claims. Gateway group selectors use synced IdP groups, not Access Groups.
- Group names and SAML/OIDC attributes are case-sensitive. Verify exact claim names and values before creating group-based rules.
- SCIM changes and group membership can be stale until sync and re-authentication complete. Troubleshoot with the user's last authenticated identity, not just the IdP state.
- Access policies are default-deny. A private app with routes but no Allow policy still blocks access.
- Access policy selectors can use IP lists, not Gateway domain or URL lists.
- SaaS federation handles authentication into the SaaS app. SaaS authorization and tenant restrictions usually require SaaS-side roles and/or Gateway tenant controls.
- Browser Rendering for SSH/VNC/RDP is an Access capability. Browser Isolation renders general web content remotely. Do not conflate them.

### Private Networking

- Split tunnel mode changes the meaning of every route decision: Exclude mode sends traffic to Cloudflare when removed from excludes; Include mode sends traffic only when added to includes.
- Virtual networks must be assigned consistently to tunnel routes and the WARP device profile. One side alone creates unreachable routes.
- CIDR routes and hostname routes solve different problems. Hostname routes still need DNS resolution through resolver policies or Local Domain Fallback.
- A healthy tunnel only proves cloudflared can reach Cloudflare. The connector must also resolve and reach the private origin.
- Run multiple cloudflared connectors for production HA, preferably on separate hosts. Token-based, remotely managed tunnels are the default for new deployments.

### Gateway, TLS, and DLP

- `dns.domains` matches a domain and subdomains; `dns.fqdn` is exact-match only.
- DNS pre-resolution selectors and post-resolution selectors do not behave like a single strict precedence list. Retrieve current evaluation docs before changing rule order.
- HTTP Do Not Inspect rules run before HTTP Allow/Block/Isolate behavior. A later block rule will not override an earlier inspection bypass.
- Certificate-pinned apps need Do Not Inspect exceptions before broad TLS inspection. Deploy the Cloudflare root CA to managed devices before enabling inspection.
- DLP profiles are detection definitions only. They do nothing until referenced by Gateway HTTP policies or CASB scan settings.
- Start DLP with monitoring/payload logging where appropriate, tune false positives, then block.
- Gateway Network policies are strict L4 controls. Identity-aware L4 matching requires authenticated WARP context.

### CASB, Risk, and Operations

- API CASB is out-of-band and periodic. It does not provide real-time inline enforcement; use Gateway/DLP/tenant controls for inline protection.
- CASB findings are tied to specific assets and instances. Drill into affected assets before recommending remediation.
- Use current Dashboard remediation guidance for CASB fixes. Most remediations happen in the SaaS admin console, not Cloudflare.
- Large SaaS integrations can take 24-48 hours for initial scans. Reauthorizing can restart scan state; check credential health before reconnecting.
- User risk scores are behavior-based and asynchronous. CASB findings do not automatically imply high user risk.

### Magic WAN / Site Connectivity

- Magic WAN is connectivity, not a security service. Apply inspection and policy with Gateway/network firewall where required.
- WAN firewall expressions are not the same language as Gateway wirefilter expressions. Retrieve the current syntax before editing.
- Generated IPsec PSKs and some OAuth/client secrets are returned once. Store them immediately.

## Output Defaults

- Designs: current assumptions, target architecture, product responsibilities, rollout phases, validation, and open decisions.
- Configuration work: prerequisites, exact resources to inspect/create/change, test cases, and rollback.
- Troubleshooting: traffic path, likely failure point, evidence to collect, and next test.

## API Safety

- Use fully qualified MCP tool names when MCP tools are available.
- Never guess category IDs, application IDs, wirefilter fields, or API request bodies. Retrieve the current schema/docs and existing account objects.
- Do not enable broad production policies without explicit approval.
