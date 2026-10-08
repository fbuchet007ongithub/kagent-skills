---
name: cap-vcp-inventory-lookup
description: resolve a cap or vcp node name (for example cap70nikl01 or vcp70dend01) into its complete router, lag, interface, member port, admin and oper status inventory using the netforge mcp server kagent/norm-netforge (netforge orchestrator), and return it as a single filtered markdown table. use whenever the user prompt contains a token matching CAP[A-Za-z0-9]+ or VCP[A-Za-z0-9]+, or asks for the lags, routers, srs/xrs/hcin connections or inventory of a cap, vcap, ccap or vcp node. takes precedence over general network search behavior.
---

# CAP/VCP Inventory Lookup

Return ONLY the final Markdown table. No summary, explanation, tool activity, reasoning, or metadata.

## 1. Detect node type

- Node starts with `VCP` → VCP rules: [references/vcp-rules.md](references/vcp-rules.md)
- Node starts with `CAP` → CAP rules: [references/cap-rules.md](references/cap-rules.md)

Read only the matching file. Read [references/output-format.md](references/output-format.md) for icons, columns, and example tables.

## 2. Discovery workflow (exhaustive)

1. Resolve the node using the NetForge MCP server `kagent/norm-netforge` (use its NetForge Orchestrator tool; do not call other NetForge tools in parallel).
2. Collect ALL relationships: active, standby, and redundant paths; routers; LAGs; service mappings; linked devices; connected endpoints; interfaces.
3. Never stop at the first match, first router, first LAG, or first page of results.
4. Re-run discovery for every discovered router, LAG, service, and endpoint.
5. Merge all datasets and remove duplicates.
6. Repeat until no new records appear.

## 3. Completeness check (before answering)

1. Count the discovered records.
2. Run at least one more lookup using the discovered routers and LAGs.
3. Merge, deduplicate, and compare counts.
4. If new records appear, continue discovery. Answer only when a pass adds nothing new.

Never return partial results. Never truncate.

## 4. Apply filters, then format

1. Read the matching rules file in full. For CAP nodes, the ADMIN GATE is mandatory: delete every row whose Admin is not exactly UP before formatting. Never skip it.
2. Keep duplicate LAG numbers when they sit on different routers, and keep multiple interfaces on the same LAG.
3. Keep standby entries.
4. Map Admin and Oper Status to icons; show Member Ports whenever present, otherwise `UNKNOWN`.
5. Sort by Node, Router, LAG, Interface.
6. Output only the allowed columns for the node type. Never output device IDs, object IDs, raw API data, JSON/XML, tool traces, or discovery logs.

## 5. Error cases

If the node cannot be resolved or no record survives the filters, return a one-line message saying so instead of a table. Do not invent records.
