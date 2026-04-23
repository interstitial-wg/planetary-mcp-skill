---
name: planetary
description: Interstitial Planetary MCP: POST /api/v1/atlas/mcp with four tools (describe_atlas, search_sensors, get_readings, describe_sensor). Sensor/env data. Use instead of legacy registry unless migrating.
---

# Planetary — public MCP (MVP)

**Planetary** is what we call the product (Planetary Software), from **Interstitial** — a single surface for **environmental observation and sensor** data. Technically, the public HTTP route uses **`atlas` in the path** and tool names like `describe_atlas`; that’s the API contract, not a separate “Atlas product name.”

## When to use

- You’re building an **agent or integration** against **Interstitial Planetary** over **MCP (HTTP + JSON)**.
- The story you should tell is: **one MCP URL, four tools** — not “four separate MCP services.”
- You need: **what’s in the DB** (`describe_atlas`), **find sensors** (`search_sensors`), **time series / latest** (`get_readings`), or **one sensor** (`describe_sensor`).

**Do not** lead with the **legacy registry** or **per-sensor upstream** routes as the main product; use them only for backward compatibility or when someone explicitly needs old tool names or a source-specific Python MCP.

---

## Public MVP (what to ship in docs and tests)

| Item | Value |
|------|--------|
| **Method / path** | `POST /api/v1/atlas/mcp` |
| **Auth** | Per deployment (e.g. API key header). Never put secrets in prompts or commits. |
| **Shape** | MCP over HTTP on this route: the model sees **exactly four tools** on this one URL. |

The path says **`atlas`**; the product name for people is **Planetary**. Both can appear in the same sentence: *“Planetary’s public MCP is `…/atlas/mcp`.”*

### Four tools (plain language)

| Tool | Role |
|------|------|
| **describe_atlas** | Coverage-style summary: counts, services, data types, optional bounds — *what’s in the DB*. |
| **search_sensors** | Find sensors: **bbox and/or point+radius**, filters, limits. |
| **get_readings** | Time series / latest reading; optional aggregation where supported. |
| **describe_sensor** | One sensor: ids, backing service, location, metadata. |

**One-liner for docs:** **Planetary** = one MCP endpoint, **four tools** — not four separate MCPs.

---

## Optional / not MVP

### Legacy registry MCP

- `POST /api/v1/sensors/registry/mcp` — older tool names (`list_sensors`, `sensor_connected_services`, `sensor_cleaned_stream`, `sensor_last_reading`).
- Use for **migrations** or when a client only knows these names. Default **Planetary** docs should use **`/api/v1/atlas/mcp`**.

### Per-sensor upstream MCP

- `POST /api/v1/sensors/:sensorId/mcp` when the sensor has a `service_id` (forwards to upstream services: e.g. ndbc, airnow, purpleair, trefle, wqp). **Many tools per source**, not the four-tool public MVP.
- Use for **deep or source-specific** work — separate from the main Planetary four-tool line.

---

## Agent behavior

1. Default to **`POST /api/v1/atlas/mcp`** and the **four** tools; pull live schemas from the server.
2. Call it **Planetary** (or Interstitial Planetary) in user-facing text; use **`atlas` in paths and legacy tool names** where the API requires it.
3. Bring up legacy or per-sensor MCP only when the user’s task requires it.

---

## Repository note

GitHub: **interstitial-systems/skills** — skill path **`skills/planetary`**. **Public** so `npx skills add interstitial-systems/skills --skill planetary` (or the tree URL to this folder) works without a private app repo. This repo **does not** host the server — it encodes how agents should work with **Planetary**’s public MCP as above.
