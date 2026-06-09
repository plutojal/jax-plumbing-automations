# Weekly: Planner Backup

Runs every Saturday at 03:00 UTC. For each of the seven Jax Plumbing planners, lists all tasks and buckets, reshapes the data, and writes two CSV files to SharePoint — one for tasks and one for the bucket reference map.

## Logic

Each planner runs its own independent chain in parallel. A failure in one planner does not block the others.

```
Initialize BackupDate variable
    │
    ├── [Servicing]       List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
    ├── [Quoted Works]    List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
    ├── [Quotes]          List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
    ├── [Breakdown]       List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
    ├── [General Jobs]    List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
    ├── [Project Install] List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
    └── [Project Quotes]  List Buckets → List Tasks → Select Tasks → Select Buckets → CSV Tasks → CSV Buckets → File Tasks → File Buckets
```

## Task CSV Columns

| Column | Planner Field |
|---|---|
| Task Title | `title` |
| Bucket ID | `bucketId` |
| Start Date | `startDateTime` |
| Due Date | `dueDateTime` |
| Percent Complete | `percentComplete` |
| Created Date | `createdDateTime` |

## Bucket CSV Columns

| Column | Planner Field |
|---|---|
| Bucket ID | `id` |
| Bucket Name | `name` |

## Planners

| Planner | Plan ID | Group ID | SharePoint Folder |
|---|---|---|---|
| Servicing | `yD_18hw4hkmq0SWujsfFmZgAA88H` | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` | Planner Backups/Servicing |
| Quoted Works | `MwV9RfoPLkybHdFwh99yRpgADla_` | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` | Planner Backups/Quoted Works |
| Quotes | `X0UloyezF0uAciITaNPmDJgACX-F` | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` | Planner Backups/Quotes |
| Breakdown | `_BYbti8PikevwupaBYfbVZgAF8yV` | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` | Planner Backups/Breakdown |
| General Jobs | `EzDCv-uoaUyKFlc-IRelbpgADYCI` | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` | Planner Backups/General Jobs |
| Project Install | `xAW30scu2E26_wE6zp2a8JgAESmS` | `f32f4bde-3d77-4bf9-bc30-ccf7ae6cff5b` | Planner Backups/Project Install |
| Project Quotes | `RFTf3QT3DkiAD6Hp-YqycpgAHw_2` | `c3477e81-429c-465f-8e1a-865400170704` | Planner Backups/Project Quotes |

## SharePoint Output

| Site | `https://jaxplumbinggy.sharepoint.com/sites/JaxPlumbing` |
|---|---|
| Tasks file | `/Shared Documents/Planner Backups/{Planner}/planner_backup_{name}_YYYY-MM-DD.csv` |
| Buckets file | `/Shared Documents/Planner Backups/{Planner}/planner_backup_{name}_YYYY-MM-DD_buckets.csv` |

## Connections

| Connection | Account |
|---|---|
| Planner | `data@jaxplumbing.co.uk` |
| SharePoint | `data@jaxplumbing.co.uk` |

## Changes from Original Export

- Removed cross-planner `runAfter` dependencies — each planner now runs its own independent chain in parallel
- Fixed `Quoted Works` task reshape: `createdBy` (wrong field) → `createdDateTime`
- Added `startDateTime` (Start Date) to all task CSVs
- Removed redundant duplicate `operationMetadataId` values from all `Create File` actions
- Removed internal flow metadata from the definition wrapper
- All `runAfter` conditions use `["Succeeded"]` only — flow halts on failure rather than continuing silently
