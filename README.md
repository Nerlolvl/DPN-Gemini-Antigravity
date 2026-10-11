# DPN — Gemini & Antigravity

Smart-routing lists for DPN: routes only Google Gemini / Antigravity traffic through the tunnel (fixes `400: User location is not supported`), everything else stays direct.

| File | Records | Purpose |
|---|---|---|
| `smartDomain.csv` | 16 | Gemini web/AI Studio, Antigravity endpoints, Google auth |
| `smartIp.csv` | 50 | IPv4 backup for the Antigravity language server |

Format: `value,EUW,ANY,EUW` — change `EUW` if your DPN region differs.

Import both files, apply, fully restart the Antigravity app.
