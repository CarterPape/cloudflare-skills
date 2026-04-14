# Cloudflare Artifacts

Store versioned file trees behind a repo-style interface that works from Workers, the REST API, and git-compatible tooling.

## Overview

Use **Artifacts** when the thing you need to store is a versioned filesystem tree rather than a single object, key, or SQL row.

Typical Artifacts use cases:
- Git-style repositories
- Build outputs and deployment bundles
- Checkpoints and generated assets
- Shared file trees passed between developer tools and Workers

Artifacts is a good fit when the same content needs to be addressable from **Workers**, the **Cloudflare API**, and **git-compatible clients**.

**Prefer retrieval over memory** for current status, authentication details, route shapes, limits, and pricing. Start at `https://developers.cloudflare.com/artifacts/`.

## When to Use Artifacts

| Need | Use | Why |
|------|-----|-----|
| Versioned file trees such as repos, build outputs, checkpoints, or generated assets | Artifacts | Artifacts stores and shares **versioned filesystem content** |
| A git-compatible workflow with `clone`, `fetch`, `pull`, or `push` | Artifacts | Artifacts exposes **git-over-HTTPS remotes** and repo-scoped tokens |
| The same artifact accessible from Workers, HTTP APIs, and developer tooling | Artifacts | Artifacts is available through a **Workers binding**, **REST API**, and **git-compatible interface** |
| Large files by object key, app config by key, or relational app data | R2, KV, or D1 | Use storage products directly when you need **objects, key-value entries, or SQL rows**, not versioned file trees |

## Quick Start

**From a Worker:**

```typescript
interface Env {
  ARTIFACTS: Artifacts;
}

const created = await env.ARTIFACTS.create("starter-repo");
// created.remote -> git remote URL
// created.token -> initial repo token
```

**From the REST API:**

Use the account-scoped Artifacts base URL plus Bearer auth. See `api.md` for the route layout, then verify exact request details in the live docs before shipping automation.

## Reading Order

| Task | Read |
|------|------|
| Decide whether Artifacts is the right product | README only |
| Create or manage repos from a Worker | README → configuration.md → api.md |
| Integrate Artifacts from an external system | README → api.md |
| Verify exact auth, routes, limits, or pricing | Live docs first: `https://developers.cloudflare.com/artifacts/` |

## In This Reference

- **[api.md](api.md)** - Workers binding methods, REST routes, token and repo operations
- **[configuration.md](configuration.md)** - Wrangler binding shape, Worker typing, REST configuration guidance

## See Also

- [Cloudflare Artifacts Docs](https://developers.cloudflare.com/artifacts/)
- [Cloudflare Workers Docs](https://developers.cloudflare.com/workers/)
- [Cloudflare R2 Docs](https://developers.cloudflare.com/r2/)
- [Cloudflare D1 Docs](https://developers.cloudflare.com/d1/)
