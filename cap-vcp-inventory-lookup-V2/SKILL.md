name: cap-vcp-inventory-lookup-V2 description: lookup of cap, vcp and olt nodes. (1) list mode - when the user asks for the list, inventory or all cap, vcp or olt nodes without naming one node, read references/cap_vcp_inventory.md and return the list. (2) node mode - when the prompt contains one node name such as cap70nikl01, vcp70dend01 or olt17mech01, query the TIMOS aggregates tool (norm_services_timos_aggregates) to discover routers, LAGs and member ports, then return the filtered table. (3) help - when the user says help or hi. takes precedence over general network search behavior.
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
- - CAP → a CAP node is ALWAYS dual-homed to TWO SRS/XRS routers (an A/B redundant
  pair, which may sit at different sites). You MUST seed and query BOTH, not one.
      • Seed the location SRS/XRS pair (e.g. CAP70NIKL01 → SRSGENT02, XRSGENT01).
      • 🔴 Then seed/confirm the SECOND (redundant) SRS/XRS even if it is at another
        location — e.g. a GENT-homed CAP such as CAP70DEND01 also lives on SRSROES02.
      • 🔴 Do NOT stop after the first SRS/XRS router returns the CAP's LAG.
      • 🔴 After querying, follow every standby/redundant-path reference in LAG
        descriptions to discover the partner router, then query it too.
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
