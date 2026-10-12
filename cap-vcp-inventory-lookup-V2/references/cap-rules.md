# CAP rules (node starts with CAP)

## Routers
Keep only routers matching `SRS*` or `XRS*` (e.g. SRSGENT02, SRSROES02, XRSGENT01, XRSGENT02).
Exclude all `HCIN*` routers and all `SR*` routers that are not SRS/XRS (e.g. SRDEND01, SRNIKL01, HCINDENDA01).

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
