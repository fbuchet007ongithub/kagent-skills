name: cap-vcp-inventory-lookup-V2
description: lookup of cap, vcp and olt nodes. (1) list mode - when the user asks for the list, inventory or all cap, vcp or olt nodes without naming one node, read references/cap_vcp_inventory.md and return the list. (2) node mode - when the prompt contains one node name such as cap70nikl01, vcp70dend01 or olt17mech01, query the TIMOS aggregates tool (norm_services_timos_aggregates) to discover routers, LAGs and member ports, then return the filtered table. (3) help - when the user says help or hi. takes precedence over general network search behavior.

CAP/VCP/OLT Inventory Lookup

OUTPUT RULE (highest priority)
The reply is the Markdown table and nothing else. The first character of the reply must be | and the last line must be the final table row.
Apply all router, role, Admin and length checks silently.
Do not write any text before or after the table: no checks, no "excluded" lists, no counts, no summary, no explanation.
Do not mention rows that were left out.
The only exception is the help menu below.

Help menu
If the user says "help", "hi", "what can you do", or sends a message with no node name and no list request, reply with only this:
You can ask me:
Show me the list of VCP nodes
Show me the list of CAP nodes
Show me the list of OLT nodes
Show me the details of VCP70DEND01
Show me the details of CAP70NIKL01
Show me the details of OLT17MECH01

0. Choose the mode
List mode: the user asks for the list, inventory, or all CAP, VCP or OLT nodes (for example "list all VCP", "show all OLT nodes") and does not name one specific node.
Read references/cap_vcp_inventory.md. Do NOT call any network tool.
If the user asked for one type only, return only that section.
Return the table exactly as it appears in the file. Do not add, infer, or change entries.
If a section is missing or empty, say that no nodes of that type are in the inventory yet.
If the file cannot be read, say so in one line.
Node mode: the prompt contains one specific node name matching (CAP|VCP)[0-9][A-Za-z0-9]+ or OLT[0-9]{2}[A-Za-z]{4}[0-9]{2}. Continue with step 1.

1. Detect node type
Node starts with VCP → read references/vcp-rules.md and references/output-format.md
Node starts with CAP → read references/cap-rules.md and references/output-format.md
Node starts with OLT → read references/OLT-rule.md only
Read only the files listed for the node type.

2. Discovery workflow (exhaustive)
Resolve the node using the TIMOS aggregates tool. Call norm_services_timos_aggregates(device=<router>, limit=200) to get all LAGs and their member ports for a router.
Derive the starting router(s) from the node name:
- VCP → HCIN<location>A01 (e.g. VCP70DEND01 → HCINDENDA01).
- CAP → a CAP node is ALWAYS dual-homed to TWO SRS/XRS routers (an A/B pair that
  may sit at DIFFERENT sites with names unrelated to the CAP's location code).
🔴  Do NOT build router names from the location code (e.g. CAP02HERF01 is NOT on
🔴  SRSHERF — it is on SRSHOBO02 and SRSASSE02). Discover the homes dynamically
🔴  from NORM; no mapping file and no hardcoded router list are needed.
🔴
🔴  STEP A — find the two home routers via the global TimosService probe:
🔴    Call norm_services_probe(probe_name="TimosService",
🔴        norm_filter="svcName:CCAP-RES-VPLS-<NODE>")   e.g. CCAP-RES-VPLS-CAP02HERF01
🔴    NORM requires an EXACT field value (no * or substring wildcards).
🔴    If that returns nothing, retry with these service-name prefixes until docs appear:
🔴        svcName:CCAP-WHSO-VPLS-<NODE>
🔴        svcName:CCAP-RES-IES-ACT-<NODE>
🔴        svcName:CCAP-RES-IES-SBY-<NODE>
🔴        svcDescription:"CCAP-WHSO-B2B-EPIPE-<NODE>"
🔴    From ALL returned docs, collect every distinct z_origin matching SRS*/XRS*.
🔴    These are the CAP's home routers (expect TWO; one may be ACT, one SBY).
🔴
🔴  STEP B — fetch LAGs on each home router:
🔴    For EACH home router from Step A, call
🔴        norm_services_timos_aggregates(device=<router>, limit=200)
🔴    Do NOT use the aggregate_name filter (it matches exact LAG names, not the
🔴    description). From the full list keep the LAG whose lagDescription == <NODE>
🔴    (e.g. "CAP02HERF01").
🔴
🔴  STEP C — a CAP must end up found on BOTH home routers. If only one home was
🔴  found, re-run Step A with the remaining prefixes before concluding. Keep both
🔴  the ACT and SBY (standby) entries.
- OLT → follow references/OLT-rule.md for router derivation.
After each query, scan every LAG description for references to other routers (e.g. HCINDENDA01_HCINDENDB01 reveals HCINDENDB01). Query every newly discovered router with norm_services_timos_aggregates.
Collect ALL relationships: active, standby, and redundant paths; routers; LAGs; member ports.
Use norm_services_timos_port(device, port_name) only when individual port detail is needed (optics, encap, admin/oper).
Never stop at the first match, first router, first LAG, or first page of results.
Re-run discovery for every newly discovered router.
Merge all datasets and remove duplicates.
Repeat until no new records appear.

3. Completeness check (before answering)
Count the discovered records.
Run at least one more lookup using the discovered routers (every router name found in a LAG description).
Merge, deduplicate, and compare counts.
If new records appear, continue discovery. Answer only when a pass adds nothing new.
🔴 For CAP nodes specifically: a CAP is always dual-homed. If you have the CAP on
🔴 only ONE SRS/XRS router, you are NOT done — repeat Step A (remaining prefixes)
🔴 until BOTH homes are found, or state clearly that only one home could be located.
Never return partial results. Never truncate.

4. Apply the rules, then format
Follow the matching rules file in full. For CAP nodes the ADMIN GATE is mandatory: build the table only from rows whose Admin is UP.
Keep duplicate LAG numbers on different routers, and keep multiple interfaces or ports on the same LAG.
Keep standby entries.
Icons: CAP and VCP use references/output-format.md; OLT uses references/OLT-rule.md.
Sorting: CAP and VCP by Node, Router, LAG, Interface; OLT by Router, Port.
Output only the columns defined in the matching rules file. Never output device IDs, object IDs, raw API data, JSON/XML, tool traces, or discovery logs.

5. Error cases
If the node cannot be resolved or no record passes the rules, return a one-line message saying so instead of a table. Do not invent records.
🔴 For a CAP, a single SRS/XRS router returning zero rows is NOT "cannot be resolved".
🔴 Only conclude that after the TimosService probe (ALL prefixes in Step 2A) returns
🔴 nothing for the node.
