# OLT rules (node starts with OLT)

## Node detection

An OLT node name matches `OLT[0-9]{2}[A-Za-z]{4}[0-9]{2}` (example: OLT17MECH01).

## Routers

Return the CIN routers that the OLT uplinks connect to (for example CINMECHA01 and CINMECHB01).
Keep the routers of BOTH sides, A and B, when both exist.
Leave out every router that is not a CIN router.

## Rows

Return one row per uplink port. Keep:
- Every port of every LAG (for example 1/1/45, 1/1/46, 1/1/47 and 1/1/48 of lag-64).
- The same LAG number on different routers (lag-64 on CINMECHA01 and on CINMECHB01).
- Every NT side (NTA and NTB).

Do not stop after the first router, the first LAG or the first page of results.
Do not return partial results.

## Columns (exactly, in this order)

Router | Port | LAG | Description | Speed | Admin | Oper

Do NOT include a Node, Interface, Member Ports or Role column.

## Status icons

Apply the same icons to the Admin and Oper columns:

- inService → ✅ inService
- outOfService → ❌ outOfService
- UP → ✅ UP
- DOWN → ❌ DOWN
- DEGRADED → ⚠️ DEGRADED
- UNKNOWN or missing → ❓ UNKNOWN

For any other value, show the value as reported, without an icon.

## Sorting

Sort by Router, then Port.

## Output

Return a Markdown table only, with nothing before or after it. Do not mention anything that was checked or left out.

| Router | Port | LAG | Description | Speed | Admin | Oper |
|--------|------|-----|-------------|-------|-------|------|
| CINMECHA01 | 1/1/45 | lag-64 | OLT17MECH01_NTA_1/1 | 10G | inService | ✅ inService |
| CINMECHA01 | 1/1/46 | lag-64 | OLT17MECH01_NTA_1/2 | 10G | inService | ✅ inService |
| CINMECHA01 | 1/1/47 | lag-64 | OLT17MECH01_NTA_1/3 | 10G | inService | ✅ inService |
| CINMECHA01 | 1/1/48 | lag-64 | OLT17MECH01_NTA_1/4 | 10G | inService | ✅ inService |
| CINMECHB01 | 1/1/45 | lag-64 | OLT17MECH01_NTB_1/1 | 10G | inService | ✅ inService |
| CINMECHB01 | 1/1/46 | lag-64 | OLT17MECH01_NTB_1/2 | 10G | inService | ✅ inService |
| CINMECHB01 | 1/1/47 | lag-64 | OLT17MECH01_NTB_1/3 | 10G | inService | ✅ inService |
| CINMECHB01 | 1/1/48 | lag-64 | OLT17MECH01_NTB_1/4 | 10G | inService | ✅ inService |
