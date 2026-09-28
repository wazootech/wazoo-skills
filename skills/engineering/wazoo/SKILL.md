---
name: wazoo
description:
  Manage Wazoo Worlds, provision knowledge graphs, execute SPARQL queries,
  search structured context, manage tokens, and clean up platform resources.
---

# Wazoo Skill

Use this skill when interacting with the Wazoo platform, managing knowledge
graph worlds, querying structured memory, or maintaining durable agent context.

## Environment setup

Wazoo uses two token types:

- `WAZOO_PLATFORM_TOKEN` (`wzp_...`): Control plane management (worlds, tokens,
  usage, billing).
- `WORLDS_TOKEN` (`wzw_...`): Data plane operations (import, search, SPARQL,
  export).

```bash
export WAZOO_PLATFORM_TOKEN="wzp_..."
export WORLDS_TOKEN="wzw_..."
```

## 1. Control plane management

### List worlds

```bash
curl -s -X GET "https://api.wazoo.dev/v1/worlds" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

### Create a world

Create a world with a display name. `worlds-api` mints one immutable
`w_<UUIDv4>` ID. The create request has display-name and optional-region fields, with no
caller-selected ID or slug field. The response exposes the ID as `world.id`; reuse it
unchanged as `WORLD_ID`. Public resource objects expose `id`, and database
primary keys use `<entity>_id`.

```bash
curl -s -X POST "https://api.wazoo.dev/v1/worlds" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "world": { "displayName": "Project Context Graph", "region": "auto" }
  }'
```

For the examples below, replace this sample with the `id` returned above:

```bash
export WORLD_ID="w_123e4567-e89b-42d3-a456-426614174000"
```

### Get world details

```bash
curl -s -X GET "https://api.wazoo.dev/v1/worlds/$WORLD_ID" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

### Update a world

```bash
curl -s -X PATCH "https://api.wazoo.dev/v1/worlds/$WORLD_ID" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "updateMask": "displayName",
    "world": { "displayName": "Project Context Graph v2" }
  }'
```

### Delete a world

```bash
curl -s -X DELETE "https://api.wazoo.dev/v1/worlds/$WORLD_ID" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

### Undelete a world

Deletes are soft and recoverable. Restore a deleted world with undelete:

```bash
curl -s -X POST "https://api.wazoo.dev/v1/worlds/$WORLD_ID/undelete" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

## 2. Token management

### List platform tokens

```bash
curl -s -X GET "https://api.wazoo.dev/v1/auth/api-tokens" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

### Create a platform token

```bash
curl -s -X POST "https://api.wazoo.dev/v1/auth/api-tokens" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-agent-token",
    "scope": "users.read worlds.read usage.read"
  }'
```

### Revoke a platform token

```bash
TOKEN_ID="<id returned when the token was created>"
curl -s -X DELETE "https://api.wazoo.dev/v1/auth/api-tokens/$TOKEN_ID" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

### List world tokens

```bash
curl -s -X GET "https://api.wazoo.dev/v1/worlds/$WORLD_ID/auth/tokens" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

### Create a world token

World tokens are scoped to a single world for data-plane access:

```bash
curl -s -X POST "https://api.wazoo.dev/v1/worlds/$WORLD_ID/auth/tokens" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ "name": "ci-data-access" }'
```

### Revoke a world token

Use the token response's `id` field.

```bash
curl -s -X DELETE "https://api.wazoo.dev/v1/worlds/$WORLD_ID/auth/tokens/{tokenId}" \
  -H "Authorization: Bearer $WAZOO_PLATFORM_TOKEN"
```

## 3. Data plane operations

### Import graph data

Import RDF quads (JSON array) or chunk text into a world:

```bash
curl -s -X POST "https://data.wazoo.dev/worlds/$WORLD_ID/import" \
  -H "Authorization: Bearer $WORLDS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "contentType": "text/plain",
    "data": "Architectural Decision Record 001: Use hybrid SPARQL and vector search for durable memory."
  }'
```

### Hybrid search across context

Search world graph memory by query text:

```bash
curl -s -X POST "https://data.wazoo.dev/worlds/$WORLD_ID/search" \
  -H "Authorization: Bearer $WORLDS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "Architectural Decision Record",
    "limit": 5
  }'
```

### Execute SPARQL query

Run graph queries over triples/quads in the world:

```bash
curl -s -X POST "https://data.wazoo.dev/worlds/$WORLD_ID/sparql" \
  -H "Authorization: Bearer $WORLDS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "SELECT ?s ?p ?o WHERE { ?s ?p ?o } LIMIT 10"
  }'
```

### Export graph data

Export world quads for auditing or local backup:

```bash
curl -s -X GET "https://data.wazoo.dev/worlds/$WORLD_ID/export?format=application/json&limit=100" \
  -H "Authorization: Bearer $WORLDS_TOKEN"
```
