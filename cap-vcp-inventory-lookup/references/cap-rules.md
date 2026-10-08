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

How to check each router, one by one:

Write the router name as separate characters and count them. Example: S-R-S-A-S-S-E-0-1 = 9.
If the count is 9 and the name starts with SRS or XRS, include its rows.
If the count is 8 or fewer, or 10 or more, leave out all of its rows.
Worked examples:

SRSASSE01 = 9 characters, included
SRSHOBO01 = 9 characters, included
SRSGENT02 = 9 characters, included
XRSGENT01 = 9 characters, included
SRSTAB01 = 8 characters (site code TAB has only 3 letters), left out
SRDEND01 = 8 characters, left out
HCINDENDA01 = 11 characters, left out
Do this check on every router before building the table. A router that fails it never appears in the output, even if its Admin is UP.

Columns (exactly, in this order)
Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status

Do NOT include a Role column.

Result set
Return every unique record that passed the ADMIN GATE and the Routers rule. Keep standby entries, DOWN or DEGRADED oper states (when Admin = UP), duplicate LAG numbers on different routers, and multiple LAGs on the same router.
