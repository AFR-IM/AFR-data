# aiapp-data

Public scan data repository for [aiapp.im](https://aiapp.im) — AI-Friendliness Rated Directory.

This repository is the **single source of truth (SSOT)** for AFR scan snapshots.
The site frontend rebuilds from this data; the D1 database can be fully rebuilt from it.

## Structure

```
scans/YYYY/MM/DD/{domain}.json   # daily scan snapshots (signals + AFR result)
corrections/                     # community PR corrections (triggers rescan)
badges/manifest.json             # badge registry
ads/active.json                  # active sponsored placements (read by Worker)
```

## Data format

Each `scans/.../{domain}.json`:

```json
{
  "signals": { "domain": "...", "fetchedAt": "...", "scannerVersion": "scanner-0.1.0", "...": "raw probes" },
  "afr":     { "score": 42, "grade": "D", "strategyTag": "Unconfigured", "layers": ["..."], "breakdown": {} }
}
```

- Engine: `packages/afr` in [imAiAPP/aiapp-web](https://github.com/imAiAPP/aiapp-web)
- Scoring is a pure function — anyone can recompute any snapshot from `signals`.

## Corrections

Open a PR adding/fixing entries under `corrections/`; merged corrections trigger a rescan of the affected domain.
