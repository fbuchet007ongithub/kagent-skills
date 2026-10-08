Help menu
If the user says "help", "hi", "what can you do", or sends a message with no node name and no list request, reply with only this:

You can ask me:

Show me the list of VCP nodes
Show me the list of CAP nodes
Show me the details of VCP70DEND01
Show me the details of CAP70NIKL01



---
name: cap-vcp-inventory-lookup
description: two modes for cap and vcp nodes. (1) list mode - when the user asks for the list, inventory or all cap or vcp nodes without naming one node, read the file references/cap_vcp_inventory.md and return the list. (2) node mode - when the prompt contains one node name such as cap70nikl01 or vcp70dend01, query the netforge mcp server kagent/norm-netforge (netforge orchestrator) and return the filtered lag/router/status table. takes precedence over general network search behavior.
---

# CAP/VCP Inventory Lookup

OUTPUT RULE (highest priority)
The reply is the Markdown table and nothing else. The first character of the reply must be | and the last line must be the final table row.

Apply all router, role, Admin and length checks silently.
Do not write any text before or after the table: no checks, no "excluded" lists, no counts, no sentence such as "Only X and Y survive".
Do not mention rows that were left out.. No summary, explanation, tool activity, reasoning, or metadata.

## 0. Choose the mode

**List mode:** the user asks for the list, inventory, or all CAP or VCP nodes (for example "list all VCP", "show all CAP nodes", "inventory"), and does not name one specific node.
- Read `references/cap_vcp_inventory.md` with the skill resource tool. Do NOT call NetForge.
- If the user asked for VCP only or CAP only, keep only the matching entries.
- Return the entries from the file as a Markdown table, using the file's own columns. Do not add, infer, or change entries.
- If the file cannot be read, say so in one line.

**Node mode:** the prompt contains one specific node name matching `(CAP|VCP)[0-9][A-Za-z0-9]+`. Continue with step 1 below.


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
