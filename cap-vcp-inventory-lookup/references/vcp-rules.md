# VCP rules (node starts with VCP)

## Routers
Keep only routers matching `HCIN*` (e.g. HCINDENDA01, HCINHOBOA01, HCINHOBOB01).
Exclude all `SRS*`, `XRS*`, and other `SR*` routers (e.g. SRSGENT02, SRSROES02, XRSGENT01, XRSGENT02, SRDEND01, SRNIKL01).

## Roles
Keep only roles matching `CP<number>` or `DP<number>` (CP0-CP3, DP0-DP3, and any other CPx/DPx).
Exclude every other role, e.g. B2B Subscriber Circuits, Multicast PIM Strap, DPI Subscriber Feed, K8S-S1, K8S-S2.

## Columns (exactly, in this order)
Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status | Role

Include the Role column.

## Result set
Return every unique Node / Router / LAG / Interface / Description / Speed / Member Ports / Admin / Oper Status / Role record.
Do not omit HCIN entries, standby entries, DOWN or DEGRADED states, duplicate LAG numbers on different routers, or multiple LAGs on the same router.
