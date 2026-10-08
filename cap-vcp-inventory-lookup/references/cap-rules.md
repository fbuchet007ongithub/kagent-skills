# CAP rules (node starts with CAP)

## ADMIN GATE (mandatory, applied last, before printing)
For EVERY row, read the Admin value as reported by NetForge, ignoring case.

Keep the row ONLY if Admin is exactly "UP".
DELETE the row if Admin is anything else: DOWN, DISABLED, SHUTDOWN, UNKNOWN, empty, missing, or any other wording.
Oper Status plays no part in this decision. A row with Admin = UP is kept even if Oper Status is DOWN or DEGRADED.
Before printing, re-read the table. If any row has an Admin column other than ✅ UP, delete it.


## Routers
Keep only routers matching `SRS*` or `XRS*` (e.g. SRSGENT02, SRSROES02, XRSGENT01, XRSGENT02).
Exclude all `HCIN*` routers and all `SR*` routers that are not SRS/XRS (e.g. SRDEND01, SRNIKL01, HCINDENDA01).

## Admin filter
Keep only records where Admin = UP.
Exclude Admin = DOWN, DISABLED, SHUTDOWN, UNKNOWN, or anything else not UP.
Oper Status UP, DOWN, and DEGRADED are all kept when Admin = UP.

## Columns (exactly, in this order)
Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status

Do NOT include a Role column.

## Result set
Return every unique Node / Router / LAG / Interface / Description / Speed / Member Ports / Admin / Oper Status record.
Do not omit SRS/XRS entries, standby entries, DOWN or DEGRADED oper states (when Admin = UP), duplicate LAG numbers on different routers, or multiple LAGs on the same router.
