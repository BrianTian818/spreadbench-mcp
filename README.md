# SpreadBench · NCSX-IS MCP Server

> Real-time MCP access to **NCSX-IS** — the issuance-spread benchmark for AI-infrastructure ("Neocloud") USD bonds. Constituents: CoreWeave, Nebius, Cipher, Galaxy Digital, Core Scientific, Applied Digital and peers. Every number is rebuilt from **SEC filings and the US Treasury par-yield curve**, and is independently recomputable.

**Endpoint**: `https://spreadbench.com/mcp` — Streamable HTTP, no auth required.

---

## Why this exists

AI-infrastructure companies have become one of the largest new issuers of high-yield USD debt, yet there is no public benchmark for what this credit complex costs. NCSX-IS fills that gap with a methodology that is fully public, reproducible, and free:

```
spread_i = (coupon_i − UST(tenor_i)) × 100 bp
NCSX-IS  = Σ(spread_i × size_i) / Σ(size_i)
```

- `coupon_i` comes from SEC filings (424B5, 10-K/10-Q), never from vendors
- `UST(tenor_i)` is the US Treasury par yield of matching tenor on the bond's **pricing date**, linearly interpolated, never extrapolated
- Weighted by original face value at issuance
- **Event-driven and point-in-time**: updated when a new bond prices — there is no daily change, and it is not a traded CDS level
- Not a credit rating, not investment advice

Current level: **NCSX-IS 432.3 bp** (secured 391.2 / unsecured 540.1), 13 bonds · 6 issuers · $28.06B face value. Version `v0.2.1-real`, archived at Zenodo [10.5281/zenodo.23983572](https://zenodo.org/records/23983572) with a `recompute.py` that reproduces the index from raw inputs (pure stdlib).

## Tools (8)

| Tool | Answers |
|---|---|
| `ncsx_latest` | The one number to quote — headline spread, sub-indices, counts, notional, as-of, DOI, `data_status` |
| `ncsx_index` | Full index definition — all tiers side by side with coverage notes |
| `ncsx_constituents` | The whole universe — all bonds: issuer, coupon, maturity, size, seniority, pricing date, matched Treasury yield, spread |
| `ncsx_bond` | Filtered lookup by issuer / ticker / note keyword (e.g. `"CoreWeave"`, `"GLXY"`, `"2031"`) — constituents have no CUSIPs |
| `ncsx_curve` | The US Treasury par-yield curve used on each pricing date (14 tenors) + history |
| `ncsx_sector` | Context: US high-yield OAS, investment-grade OAS and related indices with 30-day change |
| `ncsx_marks` | Independent second layer — quarterly fair-value marks from fund filings (SEC Form N-PORT, Level 2), converted to spread over matched Treasury |
| `ncsx_methodology` | Version, DOI, license, data sources, exact formulas, and what is explicitly NOT measured |

Every payload carries a `data_status` field: `real_issuance` means observed public issuance data and is safe to quote; anything marked `seed` / `retired_seed` is a deprecated demo series.

## Connect

**Any MCP client that supports Streamable HTTP** (Claude Desktop, Cursor, Cline, Claude Code, …):

```json
{
  "mcpServers": {
    "ncsx": {
      "type": "http",
      "url": "https://spreadbench.com/mcp"
    }
  }
}
```

No API key. No signup. The server is hosted — there is nothing to install.

Quick smoke test from a terminal:

```bash
curl -s https://spreadbench.com/api/v1/latest.json | python -m json.tool
```

## Data & license

- Index data and methodology: **CC BY 4.0** — cite the DOI (`10.5281/zenodo.23983572`, concept DOI `10.5281/zenodo.23977166` always resolves to the latest version)
- The MCP server itself is hosted at spreadbench.com; this repository is its public interface, documentation, and changelog
- Full API (no MCP needed): [openapi.json](https://spreadbench.com/openapi.json) · [llms.txt](https://spreadbench.com/llms.txt)

## Links

- Site: <https://spreadbench.com>
- Methodology: <https://spreadbench.com/methodology.html>
- Constituents: <https://spreadbench.com/constituents.html>
- Citation formats: <https://spreadbench.com/citations.html>
- Zenodo archive (data + recompute.py): <https://zenodo.org/records/23983572>

## About

Built and maintained by [SpreadBench](https://spreadbench.com) — the credit-spread benchmark for AI infrastructure debt. Issues and feedback welcome via this repo's issue tracker.
