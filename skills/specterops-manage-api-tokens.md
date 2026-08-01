---
name: Create and manage API tokens
description: List, create, and revoke BloodHound API key/ID token pairs for automation.
api: openapi/specterops-bloodhound-openapi.json
operations: [ListAuthTokens, CreateAuthToken, DeleteAuthToken]
---

# Create and manage API tokens

Use this skill to provision the non-expiring API key/ID pairs used for HMAC-signed automation.

## Auth
Requires an authenticated session (JWT bearer or an existing HMAC pair) with sufficient role.

## Steps
1. `ListAuthTokens` — `GET /api/v2/tokens` to see existing tokens.
2. `CreateAuthToken` — `POST /api/v2/tokens` to mint a new token. The response returns the token ID and
   the secret key used to compute the HMAC `Signature` — store the secret securely; it is shown once.
3. `DeleteAuthToken` — `DELETE /api/v2/tokens/{token_id}` to revoke a token.

## Notes
- Use non-personal token pairs for integrations (e.g. Splunk, ServiceNow); personal pairs for day-to-day
  use. See `authentication/specterops-authentication.yml` and
  `conventions/specterops-conventions.yml`.
- Errors return `api.error-wrapper` with `request_id`.
