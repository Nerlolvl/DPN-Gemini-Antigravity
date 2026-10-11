# DPN — Gemini & Antigravity Smart Routing Lists

Smart-routing lists for DPN that make **Google Gemini** (web, AI Studio, API) and **Google Antigravity** (desktop app / IDE) work through the DPN tunnel while everything else goes direct.

The problem these lists solve: Gemini / Antigravity backends reject requests coming from unsupported regions (`400 FAILED_PRECONDITION: User location is not supported for the API use`). Splitting only Google AI traffic through the tunnel keeps the rest of the traffic on the local connection.

## Files

### `smartDomain.csv`
Exact-match domain rules (no wildcards — they are not accepted by the importer):

```
domain,region,proto,region
```

Example line: `gemini.google.com,EUW,ANY,EUW`

Covers: `gemini.google.com`, `aistudio.google.com`, `ai.google.dev`, `push/alkalimakersuite-pa.clients6.google.com`, `generativelanguage.googleapis.com`, `cloudcode-pa.googleapis.com`, `daily-cloudcode-pa.googleapis.com` (the endpoint used by the Antigravity language server), `antigravity.google`, `antigravity-unleash.goog` (feature flags), auth (`accounts.google.com`, `oauth2.googleapis.com`), static assets (`gstatic.com`, `lh3.googleusercontent.com`).

### `smartIp.csv`
IPv4 safety net for clients that bypass domain matching (e.g. the Antigravity language server):

```
network_base_ip,region,proto,region
```

Example line: `172.217.113.0,EUW,ANY,EUW`

| Range | Lines | Why |
|---|---|---|
| `172.217.112.0/20` | 16 | `cloudcode-pa` / `daily-cloudcode-pa` API endpoints |
| `142.251.128.0/19` | 32 | `gemini.google.com` frontends |
| `34.54.84.0/24` | 1 | `antigravity-unleash.goog` |
| `216.239.32.0/24` | 1 | `antigravity.google` |

Total: **50 lines** — well below the typical DPN import limit (1260 entries).

## Usage

1. Import `smartDomain.csv` into the DPN **domain** list and `smartIp.csv` into the **IP** list.
2. Confirm the importer reports the expected record counts (16 and 50).
3. Apply/save the configuration.
4. Fully restart the Antigravity app (language server resolves endpoints and holds long-lived connections at startup).

## Notes

- Region tag `EUW` (EU West) is used on every line — change it if your DPN exit region differs.
- Keep both files in sync with the official Google ranges if endpoints change: https://www.gstatic.com/ipranges/goog.txt
- IPv6 is intentionally excluded: DPN importers commonly reject it. If your network hands out IPv6, Gemini traffic may leak past the tunnel — disable IPv6 on the client if you see geo-block errors.
