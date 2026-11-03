# Output format

The tables below only show the layout. Never copy their values into an answer.

Markdown table only. Nothing before or after it.

## Admin icons
UP → ✅ UP | DOWN → ❌ DOWN | DISABLED → 🚫 DISABLED | SHUTDOWN → 🚫 SHUTDOWN | UNKNOWN → ❓ UNKNOWN

## Oper Status icons
UP → ✅ UP | DOWN → ❌ DOWN | DEGRADED → ⚠️ DEGRADED | PARTIAL → ⚠️ PARTIAL | UNKNOWN → ❓ UNKNOWN

## Member Ports
Show as reported, e.g. `1/1 UP`, `2/2 UP`, `1/2 UP`. Use `UNKNOWN` if unavailable.
Always show when present: it explains degraded LAGs.

## Sorting
Node, then Router, then LAG, then Interface.

## VCP example
| Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status | Role |
|------|--------|-----|-----------|-------------|-------|-------------|-------|-------------|------|
| VCP70DEND01 | HCINDENDA01 | LAG-12 | lag-12 | VCP70DEND01-CP0 | UNKNOWN | 1/1 UP | ✅ UP | ✅ UP | CP0 |
| VCP70DEND01 | HCINDENDA01 | LAG-15 | lag-15 | VCP70DEND01-DP1 | UNKNOWN | 1/1 UP | ✅ UP | ✅ UP | DP1 |

## CAP example
| Node | Router | LAG | Interface | Description | Speed | Member Ports | Admin | Oper Status |
|------|--------|-----|-----------|-------------|-------|-------------|-------|-------------|
| CAP70NIKL01 | SRSGENT02 | LAG-214 | lag-214 | Residential Services | 100G | 2/2 UP | ✅ UP | ✅ UP |
| CAP70NIKL01 | XRSGENT01 | LAG-214 | lag-214 | Residential Services Standby | 100G | 1/2 UP | ✅ UP | ❌ DOWN |
