# AFR-data

Public scan data repository for [afr.im](https://afr.im) — AI-Friendliness Rated Directory.

This repository is the **single source of truth (SSOT)** for AFR scan snapshots.
The site frontend rebuilds from this data; the D1 database can be fully rebuilt from it.

## Structure

```
scans/YYYY/MM/DD/{domain}.json   # daily scan snapshots (signals + AFR result)
corrections/                     # community PR corrections (triggers rescan)
badges/manifest.json             # badge registry
ads/active.json                  # active sponsored placements (read by Worker)
scan-cursor.json                 # the Tranco cursor (see below)
```

## The Tranco cursor

`scan-cursor.json` records the highest Tranco rank the pipeline has
**attempted**:

```json
{ "lastRank": 11000, "updatedAt": "2026-11-22" }
```

It is written by the scan workflow after a segment was really scanned,
and it is what tells the next run which ranks to take. It used to be
derived from the number of snapshot files in this repo, but a rescan of
an old site writes a second file and an on-demand submission from a
rank-500,000 site writes one too — the count ran ahead of the ranks and
whole segments were skipped.

Note that the cursor is the *attempted* rank, not the *covered* one: a
domain the scanner cannot reach (blocked, no DNS, a challenge page)
leaves a hole in the coverage, and the weekly rescan retries it.

## Data format

Each `scans/.../{domain}.json`:

```json
{
  "signals": { "domain": "...", "fetchedAt": "...", "scannerVersion": "scanner-0.1.0", "...": "raw probes" },
  "afr":     { "score": 42, "grade": "D", "strategyTag": "Unconfigured", "layers": ["..."], "breakdown": {} }
}
```

- Engine: `packages/afr` in [AFR-IM/AFR-web](https://github.com/AFR-IM/AFR-web)
- Scoring is a pure function — anyone can recompute any snapshot from `signals`.

## Corrections

Open a PR adding/fixing entries under `corrections/`; merged corrections trigger a rescan of the affected domain.
