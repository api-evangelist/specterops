---
name: Run a Cypher query against the BloodHound graph
description: Authenticate to BloodHound and run an openCypher query to explore identity attack paths.
api: openapi/specterops-bloodhound-openapi.json
operations: [Login, GetSelf, RunCypherQuery]
---

# Run a Cypher query against the BloodHound graph

Use this skill to query the BloodHound identity attack-path graph programmatically.

## Auth
Authenticate with either a JWT bearer token or an HMAC-signed API key/ID pair.
- JWT: `POST /api/v2/login` (`Login`) with email + password (+ 2FA); use the returned token as
  `Authorization: Bearer $JWT` (valid 8 hours).
- HMAC (recommended for automation): send `Authorization: bhesignature $TOKEN_ID`, `RequestDate:
  $RFC3339`, and `Signature: $BASE64_HMAC` — an HMAC-SHA-256 chain over method+URI, the RFC3339 date to
  the hour, then the body. See `authentication/specterops-authentication.yml`.
- Confirm the session with `GET /api/v2/self` (`GetSelf`).

## Steps
1. `GetSelf` — verify credentials and the caller's roles/permissions.
2. `RunCypherQuery` — `POST /api/v2/graphs/cypher` with an openCypher `query` in the body. Keep queries
   read-only for exploration; large queries are bounded by server memory limits.
3. Handle results as graph nodes/edges. On failure, read the `api.error-wrapper` body
   (`errors[].message`, `request_id`) — see `errors/specterops-problem-types.yml`.

## Notes
- Cypher syntax support: see the BloodHound "Supported Cypher Syntax" docs.
- Respect HTTP 429 (rate limiting) with backoff.
