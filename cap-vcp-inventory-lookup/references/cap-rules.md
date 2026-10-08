CAP rules (node starts with CAP)
ADMIN GATE (mandatory)
Build the final table from rows whose Admin value is UP (ignoring case) and nothing else.

Include a row only if its Admin value is UP.
Leave out every other row: Admin DOWN, DISABLED, SHUTDOWN, UNKNOWN, empty, missing, or any other wording. These rows never appear in the output.
Oper Status plays no part in this decision. A row with Admin = UP is included even when Oper Status is DOWN or DEGRADED.
Before printing, check the Admin column of the table. Every row must show ✅ UP. If any row shows something else, do not print it.
Routers (length check, mandatory)
A router is included only if its name has EXACTLY 9 characters (letters or digits) AND starts with SRS or XRS.

Fixed shape of a valid router: 3-letter prefix (SRS or XRS) + 4-letter site code + 2-digit number. Example: SRS + ASSE + 01 = SRSASSE01.

How to check (do this silently, never write it in the answer):

Count the characters of each router name. Include its rows only if the count is 9 and the name starts with SRS or XRS.
Reference: SRSASSE01, SRSHOBO01, SRSGENT02, XRSGENT01 = 9 (valid). SRSTAB01, SRDEND01 = 8 (not valid). HCINDENDA01 = 11 (not valid).
Never mention a router or LAG that was left out. Never list the routers or LAGs you checked. A router that fails the check never appears in the output, even if its Admin is UP.

Columns (exactly, in this order)
Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status

Do NOT include a Role column.

Result set
Return every unique record that passed the ADMIN GATE and the Routers rule. Keep standby entries, DOWN or DEGRADED oper states (when Admin = UP), duplicate LAG numbers on different routers, and multiple LAGs on the same router.
