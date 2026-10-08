CAP rules (node starts with CAP)
ADMIN GATE (mandatory)
Build the final table from rows whose Admin value is UP (ignoring case) and nothing else.

Include a row only if its Admin value is UP.
Leave out every other row: Admin DOWN, DISABLED, SHUTDOWN, UNKNOWN, empty, missing, or any other wording. These rows never appear in the output.
Oper Status plays no part in this decision. A row with Admin = UP is included even when Oper Status is DOWN or DEGRADED.
Before printing, check the Admin column of the table. Every row must show ✅ UP. If any row shows something else, do not print it.
Routers
Include a row only if its router name meets BOTH conditions:

The name starts with SRS or XRS.
The name is exactly 9 characters long, all letters or digits (pattern ^(SRS|XRS)[A-Za-z0-9]{6}$). Count the router name only, without any domain suffix.
Examples:

Included: SRSGENT02, SRSROES02, XRSGENT01, XRSGENT02
Left out (not 9 characters): SRDEND01, SRNIKL01, HCINDENDA01
Left out (wrong prefix): any HCIN* router and any SR* router that is not SRS/XRS
A router name shorter or longer than 9 characters never appears in the output.

Columns (exactly, in this order)
Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status

Do NOT include a Role column.

Result set
Return every unique record that passed the ADMIN GATE and the Routers rule. Keep standby entries, DOWN or DEGRADED oper states (when Admin = UP), duplicate LAG numbers on different routers, and multiple LAGs on the same router.
