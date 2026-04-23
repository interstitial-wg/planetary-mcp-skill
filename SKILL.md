---
name: ps-atlas-mcp
description: Use Planet Species (PS) Atlas public HTTP MCP—one URL exposes exactly four tools (describe_atlas, search_sensors, get_readings, describe_sensor) for DB coverage, sensor discovery, readings, and per-sensor detail. For MVP docs and integrators; prefer this over legacy registry names or per-sensor upstream MCPs unless backward-compat or source-specific tools are required.
---

# Planet Species Atlas — public MCP (MVP)

## When to use

- Integrating an agent or app with **Atlas env observation data** via **Model Context Protocol over HTTP**.
- The product story to document and test is: **one Atlas MCP entrypoint, four tools** — not “four separate MCP services.”
- You need: **what’s in the DB** (`describe_atlas`), **find sensors** (`search_sensors`), **time series / latest** (`get_readings`), or **one sensor’s full detail** (`describe_sensor`).

**Do not** present the **legacy registry** or **per-sensor upstream** surfaces as the primary MVP; use them only when the user explicitly needs old tool names or source-specific Python MCP catalogs.

---

## Public MVP surface (authoritative for agents)

| Item | Value |
|------|--------|
| **Method / path** | `POST /api/v1/atlas/mcp` |
| **Auth** | As required by the deployment (e.g. API key header); never commit secrets. |
| **Shape** | Standard JSON-RPC style MCP over HTTP for this route — the LLM sees **exactly four tools** on this URL. |

### Four tools (plain language)

| Tool | Role |
|------|------|
| **describe_atlas** | Coverage-style summary: counts, services, data types, optional bounds — *what’s in the DB at a glance*. |
| **search_sensors** | Find sensors: **bbox and/or point+radius**, filters, limits. |
| **get_readings** | Time series / latest reading; optional aggregation **where the implementation supports it**. |
| **describe_sensor** | One sensor: ids, backing service, location, metadata. |

**Headline for docs:** one Atlas MCP endpoint, **four tools** — not “four MCP services.”

---

## Optional / not MVP (use only when needed)

### Legacy registry MCP (backward compatibility)

- **Path:** `POST /api/v1/sensors/registry/mcp` (same backend, **old tool names**).
- **Tools:** `list_sensors`, `sensor_connected_services`, `sensor_cleaned_stream`, `sensor_last_reading`.
- **Use when:** migrating old clients or the user names these tools explicitly. **MVP documentation should point integrators to** `POST /api/v1/atlas/mcp` **only** unless they need the legacy contract.

### Per-sensor upstream MCP (power users / source-specific)

- **Path:** `POST /api/v1/sensors/:sensorId/mcp` **when** the sensor has a `service_id` (forwards to registered upstream Python MCP services: e.g. ndbc, airnow, purpleair, trefle, wqp).
- **Shape:** **Many tools per upstream**, not the four-tool public MVP.
- **Use when:** deep testing or source-specific behavior; **separate** from the single public four-tool product story.

---

## Agent behavior

1. For **default Atlas integration**, assume **only** `POST /api/v1/atlas/mcp` and the **four** tools above; read tool schemas from the live MCP response.
2. Mention **legacy** or **per-sensor** paths only if the user asks for old names, migration, or upstream-specific tool catalogs.
3. After implementation phases land, re-verify **MVP** against: four tools, reliability, limits, observability, and public docs — not the optional surfaces unless scoped.

---

## Repository note

This skill is published separately so public agents can install it with `npx skills add` without access to a private monorepo. It does **not** host the server; it **documents how to work with** the public Atlas MCP as specified above.
