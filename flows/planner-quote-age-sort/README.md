# Daily: Auto-Sort Quote Tasks by Age

Runs daily at 01:00 UTC. Lists all tasks in the Quotes Planner and moves each task forward through age-banded buckets based on how long ago it was started (`startDateTime`).

## Logic

The pipeline is deliberately sequential — each stage completes before the next filter runs, preventing a task that is already being evaluated from being picked up twice in the same run.

```
List Tasks
    │
    └─► Filter: Quote Sent (>7 days old?) ──► Move to Quote Over 7 Days
    └─► Filter: Quote Over 7 Days (>14 days old?) ──► Move to Quote Over 14 Days
    └─► Filter: Quote Over 14 Days (>28 days old?) ──► Move to Quote Over 28 Days
    └─► Filter: Quote Over 28 Days (>40 days old?) ──► Move to Quote Over 40 Days
    └─► Filter: Quote Over 40 Days (>80 days old?) ──► Move to Cold Quotes
```

Age thresholds are calculated from `startDateTime` (the date the task was created / the quote was sent).

## Planner Details

| Property | Value |
|---|---|
| Plan | Quotes |
| Plan ID | `X0UloyezF0uAciITaNPmDJgACX-F` |
| Group ID | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` |

## Buckets

| Bucket Name | Bucket ID | Age Threshold | Promoted To |
|---|---|---|---|
| Quote Sent | `TsBqycM-ZUmrOWpCnaOLzJgAEtnB` | > 7 days | Quote Over 7 Days |
| Quote Over 7 Days | `LC9GSidBn0Srn_qT-yz3FZgAEuQZ` | > 14 days | Quote Over 14 Days |
| Quote Over 14 Days | `nWK8zxDc6EKMObN6K9UzUJgACS8Y` | > 28 days | Quote Over 28 Days |
| Quote Over 28 Days | `HbCiwNHWLUm1NMU5JDbBrJgAIzyE` | > 40 days | Quote Over 40 Days |
| Quote Over 40 Days | `BoorymTYSEW6OQbyMewRAJgADVAP` | > 80 days | Cold Quotes |
| Cold Quotes | `T6lBEZ_UHUWafN-Wx2g_NZgABn7i` | — | Final stage |

## Connection

Requires the **Planner** connection authenticated as `data@jaxplumbing.co.uk`.

## Changes from Original Export

- Removed unused `List Buckets` action (output was never referenced)
- Removed dead `Filter: Cold Quotes` action (no loop followed it)
- Fixed missing `runAfter` dependency on `List Tasks` for the first filter step
- All `runAfter` conditions changed from `["Succeeded", "Failed"]` to `["Succeeded"]` — actions now halt on failure rather than continuing silently
- All action names updated to match the project naming convention (`Verb_-_Plan_-_Bucket`)
