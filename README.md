# Procurement & Manufacturing API (mock)

Mock APIs for the Kong gateway demo — vendors, raw materials, purchase orders,
BOMs, production runs, quality inspections, equipment and contracts.
Complements the existing `orders` / `shipments` APIs.

## 1. Host it on Typicode (My JSON Server)

`my-json-server.typicode.com` serves any public GitHub repo's `db.json` as a REST API — no deploy needed.

1. Create a **public** GitHub repo, e.g. `procurement-api`.
2. Push `db.json` to the repo root.
3. Your base URL becomes:
   `https://my-json-server.typicode.com/<github-user>/procurement-api`

> Note: My JSON Server's free tier caps records at **5 fields each** (and this set
> at 5 resources). Records here are trimmed to their 5 most useful fields. Run
> `npx json-server db.json` locally to lift both limits and add richer records or
> more resources (equipment, contracts, bill-of-materials, etc.).

Auto-generated routes (from the top-level keys in `db.json`):

| Resource            | Endpoint                       |
|---------------------|--------------------------------|
| Vendors             | `/vendors`, `/vendors/9001`    |
| Raw materials       | `/raw-materials`               |
| Purchase orders     | `/purchase-orders`             |
| Production runs     | `/production-runs`             |
| Quality inspections | `/quality-inspections`         |

Supports filters & relations out of the box, e.g.
`/raw-materials?vendorId=9001`, `/purchase-orders?status=approved`.

> Local alternative: `npx json-server db.json` runs the same API on `http://localhost:3000`.

## 2. Upload to Kong

Edit `servers[0].url` in `procurement-api.yaml` to your My JSON Server URL first.

**Konnect (SaaS UI):** Dev Portal / API Products → *Import OpenAPI* → upload `procurement-api.yaml`.

**decK (declarative):**
```bash
deck file openapi2kong -s procurement-api.yaml -o kong.yaml
deck gateway sync kong.yaml
```

**Admin API (one service + route):**
```bash
curl -i http://localhost:8001/services \
  --data name=procurement-api \
  --data url=https://my-json-server.typicode.com/<github-user>/procurement-api
curl -i http://localhost:8001/services/procurement-api/routes \
  --data 'paths[]=/procurement'
```
