# Daily: Auto-Sort CRM Project Quote Tasks by Age

Runs daily at 01:00 UTC. Lists all tasks in the Directors Hub CRM Project Quotes Planner and moves each task forward through age-banded buckets based on how long ago it was started (`startDateTime`).

## Logic

The pipeline is deliberately sequential — each stage completes before the next filter runs, preventing a task from being double-promoted within the same daily run.

```
List Tasks
    │
    └─► Filter: Quotes Sent - Awaiting Decision (>14 days old?) ──► Move to Over 14 Days
    └─► Filter: Over 14 Days (>30 days old?) ──────────────────► Move to Over 30 Days
    └─► Filter: Over 30 Days (>60 days old?) ──────────────────► Move to Over 60 Days
    └─► Filter: Over 60 Days (>90 days old?) ──────────────────► Move to Over 90 Days / Cold Quotes
```

Age thresholds are calculated from `startDateTime` (the date the quote was sent).

## Planner Details

| Property | Value |
|---|---|
| Plan | Directors Hub CRM Project Quotes |
| Plan ID | `RFTf3QT3DkiAD6Hp-YqycpgAHw_2` |
| Group | Jax Plumbing Directors Hub |
| Group ID | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` |

## Buckets

| Bucket Name | Bucket ID | Age Threshold | Promoted To |
|---|---|---|---|
| Quotes sent - Awaiting decision | `OIqBxrQSHEOFqklRn_ToQpgAP9Eh` | > 14 days | Over 14 days |
| Over 14 days | `WkRv-MjVzEeUPflyQgsX6pgAFkbs` | > 30 days | Over 30 days |
| Over 30 days | `XNUKh98aTEKqFUyPRvShpJgAEpm6` | > 60 days | Over 60 days |
| Over 60 days | `iTd4wOa9Gk-rraBOzsdccJgAIhjK` | > 90 days | Over 90 days / cold quotes |
| Over 90 days / cold quotes | `qA2LftY32EGGeRVkxGdolJgAFvEr` | — | Final stage |

## Other Buckets in This Planner (not touched by this flow)

| Bucket Name | Bucket ID |
|---|---|
| NEW ENQUIRY - REVIEW | `xYcc_Fn3AUO1mTBptCxHuZgAKQRF` |
| REVIEWED - READY TO QUOTE | `hMjOGnPL80ysQtxAZNtJnpgADOBc` |
| AWAITING SUPPLIER PRICES | `-V65vK7ojE2XGyBj6lctvZgAKFOJ` |
| READY TO SEND | `fKBHL6uBikiQXRE26YzRUpgAGP-C` |
| Quote Accepted | `e2FwvZaDY0msp_ndn9eEApgAMD0x` |
| Quote Declined | `dQ1CUeYVUU27h9wy7vi8spgADXMF` |
| QUOTES TO REPRICE | `7aot8eSlKEmsPikAcSGjoZgADPSf` |

## Connection

Requires the **Planner** connection authenticated as `data@jaxplumbing.co.uk`.
