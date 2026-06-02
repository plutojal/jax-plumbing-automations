# planner-daily-overdue-sort

Runs daily and automatically moves overdue tasks out of the **Booked-In** bucket into the **Overdue** bucket across all five Planner boards.

## Trigger

Scheduled — runs every day at 17:00.

## Logic

For each of the five Planner boards:
1. List all tasks in the board
2. Filter to tasks in the **Booked-In** bucket
3. For each task: check if due date is in the past AND task is not completed
4. If so: move the task to the **Overdue** bucket

## Planner boards covered

| Board | Plan ID |
|---|---|
| Quotes | `X0UloyezF0uAciITaNPmDJgACX-F` |
| Quoted Works | `MwV9RfoPLkybHdFwh99yRpgADla_` |
| General Jobs | `EzDCv-uoaUyKFlc-IRelbpgADYCI` |
| Servicing | `yD_18hw4hkmq0SWujsfFmZgAA88H` |
| Breakdown | `_BYbti8PikevwupaBYfbVZgAF8yV` |

**Group ID (shared across all boards):** `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b`

## Bucket IDs

| Board | Booked-In bucket | Overdue bucket |
|---|---|---|
| Quotes | `OMOkdux-b0aUCquX8MWpCZgAFzK1` | `ev-PbxtcdkuR4gxcn90VfZgAG8ry` |
| Quoted Works | `Ka7q_0Wxd0acFxdTJYoma5gAK_uf` | `H8QAFqomhUGP-1ydsQ3N_pgACTq5` |
| General Jobs | `eKGHRNjO502U6rsA29Z1HZgAJ7yo` | `cnQI_Lr_o0Wf6ccCoCS-N5gAA7Fo` |
| Servicing | `pmYYWMpfjU2smdrIB_X6GJgAD0GZ` | `JV1u_hCiu0S_bIaMTLJOOZgAKla8` |
| Breakdown | `15xBAFkHnUe_ili_YX91XZgANytD` | `hUymETVu-0ipgFsrIrx_9pgAOrBn` |

## Connection

**Connector:** Microsoft Planner  
**Account:** data@jaxplumbing.co.uk

## Status

- [x] Built in Power Automate
- [x] Tested
- [x] definition.json exported and committed

## Change log

| Date | Change | Author |
|---|---|---|
| 2026-06-02 | Initial export and commit | |
