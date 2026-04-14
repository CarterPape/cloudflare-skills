# Artifacts Configuration

## Worker Binding

Bind Artifacts in `wrangler.jsonc`:

```jsonc
{
  "artifacts": [
    {
      "binding": "ARTIFACTS",
      "namespace": "default"
    }
  ]
}
```

This exposes Artifacts on `env.ARTIFACTS` inside your Worker.

## TypeScript

Regenerate Worker types after adding the binding:

```bash
npx wrangler types
```

Use the generated binding type in your environment definition:

```typescript
interface Env {
  ARTIFACTS: Artifacts;
}
```

Wrangler generates the `Artifacts` type from the binding. Do not hand-roll the type unless you have to.

## REST Configuration

For external systems, configure the account-scoped base URL and Bearer auth:

```bash
export ARTIFACTS_ACCOUNT_ID="<YOUR_ACCOUNT_ID>"
export ARTIFACTS_BASE_URL="https://api.cloudflare.com/client/v4/accounts/${ARTIFACTS_ACCOUNT_ID}/artifacts"
export ARTIFACTS_NAMESPACE="default"
export ARTIFACTS_JWT="<YOUR_ARTIFACTS_JWT>"
```

Use environment variables or your secret manager. Do not hardcode repo tokens or API credentials.

## Repo Tokens

Artifacts workflows usually involve repo-scoped tokens returned by `create()` or minted later through the binding or REST API.

Recommended handling:
- Mint the narrowest scope you need: `read` or `write`
- Prefer short-lived tokens for handoff between systems
- Revoke tokens that are no longer needed

Verify the current token behavior and auth guidance in `https://developers.cloudflare.com/artifacts/` before building long-lived automation.

## Git Consumers

Artifacts is designed to work with standard git-over-HTTPS clients once you have a repo `remote` and an access token.

Keep the control plane and data plane separate:
- Use the **Workers binding** or **REST API** to create repos and mint tokens
- Use the returned **git remote** in downstream tooling that needs to clone, fetch, pull, or push

## Retrieval Checklist

Check the live docs before relying on:
- exact token formats
- availability or product status
- route details for import and fork flows
- platform limits or pricing
