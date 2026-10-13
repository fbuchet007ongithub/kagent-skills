# CAP rules (node starts with CAP)

## Routers
Keep only routers matching `SRS*` or `XRS*` (e.g. SRSGENT02, SRSROES02, XRSGENT01, XRSGENT02).
Exclude all `HCIN*` routers and all `SR*` routers that are not SRS/XRS (e.g. SRDEND01, SRNIKL01, HCINDENDA01).

## Router Discovery for CAP nodes
CAP nodes are NOT homed on routers named after their own location code.
Do NOT derive the router name from the CAP node location (e.g. do NOT try SRSKORT01 for CAP14KORT01).

**Primary CAP home routers (query ALWAYS in parallel):**
SRSASSE01, SRSASSE02, SRSGENT01, SRSGENT02, SRSHASS01, SRSHASS02, SRSHOBO01, SRSHOBO02, SRSNOCM01, SRSROES01, SRSROES02, XRSASSE01, XRSASSE02, XRSGENT01, XRSGENT02, XRSHASS01, XRSHASS02, XRSHOBO01, XRSHOBO02

For each CAP node, query all 19 routers above via norm_services_timos_aggregates(device=<router>, limit=200) in parallel. Search each LAG description for the CAP node name (e.g., "CAP05SCHO05") to identify which router(s) it is homed on. A CAP must be found on at least TWO routers (Active and Standby/Backup).
Only return results from routers where the CAP node is actually found.
Continue exhaustive discovery as per the standard workflow (scan discovered LAG descriptions for cross-references to other routers).

## Admin filter
Keep only records where Admin = UP.
Exclude Admin = DOWN, DISABLED, SHUTDOWN, UNKNOWN, or anything else not UP.
Oper Status UP, DOWN, and DEGRADED are all kept when Admin = UP.

## Service-oriented entries only
Keep service entries such as: RES, VOD, EOS, WHSO, WHSW, WHSO-B2B, WHSPB, BSOD/WHS, RES/VOD/EOS/WHSO, CCAP-ACT/EOS/HOMESPOT/QAM, CCAP-SBY/EOS/HOMESPOT/QAM.
Keep Active and Standby service entries.
Exclude internal implementation entries: `CAPxxxx / DP0..DP3 Plane` and `CAPxxxx / DP0..DP3 Standby`.
When a LAG has both a service entry and an internal-plane entry, return only the service entry.

## Columns (exactly, in this order)
Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status
Do NOT include a Role column.

## Result set
Return every unique Node / Router / LAG / Interface / Description / Speed / Member Ports / Admin / Oper Status record.
Do not omit SRS/XRS entries, standby entries, DOWN or DEGRADED oper states (when Admin = UP), duplicate LAG numbers on different routers, or multiple LAGs on the same router.
