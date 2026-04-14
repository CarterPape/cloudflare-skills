# Artifacts API Reference

Use Artifacts through either the **Workers binding** or the **Cloudflare REST API**.

**Prefer retrieval** for exact request and response details. The docs draft is moving quickly, so verify current behavior at `https://developers.cloudflare.com/artifacts/` before relying on specific auth flows, token formats, or route details.

## Workers Binding

Artifacts exposes a Worker binding on `env.ARTIFACTS`.

### Namespace Methods

| Method | Use For |
|--------|---------|
| `create(name, opts?)` | Create a repo and receive its initial remote and token |
| `get(name)` | Resolve a repo handle for repo-scoped operations |
| `list(opts?)` | List repos in a namespace |
| `delete(name)` | Delete a repo |
| `import(name, source)` | Import a repo from GitHub into Artifacts |

```typescript
const created = await env.ARTIFACTS.create("starter-repo");
const repo = await env.ARTIFACTS.get("starter-repo");
const page = await env.ARTIFACTS.list({ limit: 10 });
```

### Repo Handle Methods

Use a repo handle returned by `get()`, `create()`, `import()`, or `fork()`.

| Method | Use For |
|--------|---------|
| `info()` | Read repo metadata, including the remote URL |
| `createToken(scope?, ttl?)` | Mint a repo-scoped read or write token |
| `listTokens()` | Inspect active tokens |
| `validateToken(token)` | Check whether a token is still valid |
| `revokeToken(tokenOrId)` | Revoke a token by ID or value |
| `fork(target)` | Fork one repo into another |

```typescript
const repo = await env.ARTIFACTS.get("starter-repo");
if (!repo) throw new Error("Repo not found");

const info = await repo.info();
const token = await repo.createToken("read", 3600);
```

### Import Behavior

The docs draft shows two slightly different import entry points:
- The **Workers binding** accepts either `owner/repo` shorthand or a full GitHub URL.
- The **REST API** examples use a full GitHub URL.

Verify current import requirements in the live docs before depending on shorthand outside the Workers binding.

## REST API

Artifacts uses the account-scoped Cloudflare v4 API:

```txt
https://api.cloudflare.com/client/v4/accounts/{accountId}/artifacts
```

Requests use Bearer authentication.

### Repo Routes

| Route | Use For |
|-------|---------|
| `POST /namespaces/:namespace/repos` | Create a repo |
| `GET /namespaces/:namespace/repos` | List repos |
| `GET /namespaces/:namespace/repos/:name` | Read repo metadata and remote |
| `DELETE /namespaces/:namespace/repos/:name` | Delete a repo |
| `POST /namespaces/:namespace/repos/:name/fork` | Fork a repo |
| `POST /namespaces/:namespace/repos/:name/import` | Import a GitHub repo |

```bash
curl "$ARTIFACTS_BASE_URL/namespaces/$ARTIFACTS_NAMESPACE/repos" \
  --header "Authorization: Bearer $ARTIFACTS_JWT" \
  --header "Content-Type: application/json" \
  --data '{"name":"starter-repo"}'
```

### Token Routes

| Route | Use For |
|-------|---------|
| `GET /namespaces/:namespace/repos/:name/tokens` | List repo tokens |
| `POST /namespaces/:namespace/tokens` | Create a token for a repo |
| `POST /namespaces/:namespace/tokens/validate` | Validate a token |
| `POST /namespaces/:namespace/tokens/revoke` | Revoke a token |

Use **read** tokens for clone/fetch/pull style access and **write** tokens when a workflow needs to push or otherwise mutate a repo.

## Git-Compatible Access

Artifacts returns repo `remote` URLs that work with standard git-over-HTTPS tooling.

Use Artifacts when you need to:
- hand a repo remote to another tool
- mint short-lived repo access tokens
- move versioned file trees between Workers and external systems

For exact git auth patterns and token handling, verify the current docs at `https://developers.cloudflare.com/artifacts/`.
