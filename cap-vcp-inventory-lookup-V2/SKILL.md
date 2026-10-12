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
- CAP → a CAP node is ALWAYS dual-homed to TWO SRS/XRS routers (an A/B redundant
  pair, which may sit at DIFFERENT sites with names unrelated to the CAP's own
  location code). You MUST find and query BOTH.
🔴    • WARNING: do NOT assume the CAP's location string maps to a same-named SRS/XRS
🔴      router. It often does NOT. Example: CAP02HERF01 is NOT on SRSHERF/XRSHERF
🔴      (those do not exist); it is homed on SRSHOBO02 and SRSASSE02.
🔴    • Preferred method — find the homes by the LAG description:
🔴        1. Use references/cap_sites.md (site → SRS/XRS home-pair map) if present to
🔴           look up the two home routers for the CAP's location. Query both.
🔴        2. If no mapping exists, or the mapped routers return nothing, search the
🔴           candidate SRS/XRS routers for a LAG whose description == the node name
🔴           (e.g. "CAP02HERF01"). The routers that host that LAG ARE the home pair.
🔴    • If a derived/seeded router returns ZERO rows for the CAP, DO NOT conclude
🔴      "not found". Treat the empty result as a miss and widen the search to other
🔴      SRS/XRS routers until the CAP's LAG is located on TWO routers.
🔴    • Do NOT stop after the first SRS/XRS router returns the CAP's LAG; the partner
🔴      router (second home) must also be found and queried.
🔴    • After querying, follow every standby/redundant-path reference in LAG
🔴      descriptions to confirm both homes.
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
🔴 For CAP nodes specifically: if you have found the CAP on only ONE SRS/XRS router,
🔴 you are NOT done — a CAP is always dual-homed; keep searching until BOTH homes are
🔴 found (or state clearly that only one home could be located).
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
🔴 Important: a single SRS/XRS router returning zero rows is NOT by itself "cannot be
🔴 resolved" for a CAP — only conclude that after searching for the node-name LAG
🔴 across the SRS/XRS routers and still finding nothing.
