# First Normal Form (1NF) — Explanation

## Which columns violated 1NF?

The raw CSV stored multiple values inside single cells, separated by the `|`
character. Seven columns violated the 1NF rule that every cell must contain
only one value:

| Column | Example of violation |
|---|---|
| `WorkerSkills` | `Carpentry\|Framing` |
| `WorkerCertifications` | `OSHA\|First Aid` |
| `SupplierPhones` | `617-555-9000\|617-555-9001` |
| `MaterialSupplied` | `Concrete\|Steel` |
| `MaterialUnitCost` | `120\|300` (paired by position with `MaterialSupplied`) |
| `EquipmentUsed` | `Crane\|Bulldozer` |
| `EquipmentRentalCost` | `5000\|3000` (paired by position with `EquipmentUsed`) |

The remaining columns — `ProjectID`, `ProjectName`, `ProjectType`,
`StartDate`, `EndDate`, `SiteAddress`, `SiteCity`, `SiteState`, `ClientName`,
`ClientPhone`, `ClientEmail`, `ClientCity`, `SupervisorName`,
`SupervisorPhone`, `WorkerName`, `WorkerPhone`, `WorkerHourlyRate`,
`SupplierName`, and `SupplierCity` — already held single atomic values and
did not violate 1NF.

Note also that `MaterialUnitCost` and `EquipmentRentalCost` were not merely
multi-valued: they were **positionally paired** with `MaterialSupplied` and
`EquipmentUsed`. The first cost belonged to the first material, the second
cost to the second material, and so on. This positional encoding is fragile —
if either list were reordered, the pairing would break — and it is a clear
sign that the data was not atomic.

## How did we make the values atomic?

Two techniques were used:

1. **Splitting a multi-valued cell into multiple rows.**
   For instance, the row that has `Mike Ross` in it with
   and the other carrying carpentry.‘WorkerSkills' is broken down into two rows: one with ‘and', and the other with ‘carpentry'.
   Two with a 'CARPENTRY' and one with a 'FRAMING'. This technique was used for
   `WorkerSkills`, `WorkerCertifications`, `SupplierPhones`,
   `MaterialSupplied`, and `EquipmentUsed`.

2. **Keeping paired values on the same atomic row.**
   `MaterialUnitCost` and `EquipmentRentalCost` were stored as lists parallel
   to their material/equipment names. We preserved the intended pairing by
   placing each material together with its cost on one row
   (e.g. `P001, Concrete, 120`) and each equipment item together with its
   rental cost on one row (e.g. `P001, Crane, 5000`). The formerly positional
   relationship is now explicit and atomic.

## Did you create new rows, new tables, or both?

**Both.**

- **New rows** were created inside tables where a single entity had multiple
  values. For example, a worker with two skills now appears as two rows in
  `worker_skills_1nf`, and a project with two materials appears as two rows in
  `project_materials_1nf`.

- **New tables** were created where the multi-valued fact belonged to a
  different entity than the project row it originally sat on. Specifically:

  | New table | Purpose |
  |---|---|
  | `project_workers_1nf` | Links projects to the workers assigned to them |
  | `worker_skills_1nf` | Atomic skills per worker |
  | `worker_certifications_1nf` | Atomic certifications per worker |
  | `supplier_phones_1nf` | Atomic phone numbers per supplier |
  | `project_materials_1nf` | Atomic materials and unit costs per project |
  | `project_equipment_1nf` | Atomic equipment and rental costs per project |
  | `project_suppliers_1nf` | Links projects to the suppliers they used |

  The main project table (`projects_1nf`) retained the project, client, and
  supervisor columns because none of those contained multi-valued cells.

## What key or combination of keys identifies each row?

Every 1NF table has a primary key (simple or composite) that uniquely
identifies each row:

| Table | Primary key |
|---|---|
| `projects_1nf` | `ProjectID` |
| `project_workers_1nf` | `(ProjectID, WorkerName)` |
| `worker_skills_1nf` | `(ProjectID,WorkerName, Skill)` |
| `worker_certifications_1nf` | `(ProjectID,WorkerName, Certification)` |
| `supplier_phones_1nf` | `(SupplierName, SupplierPhone)` |
| `project_materials_1nf` | `(ProjectID, Material)` |
| `project_equipment_1nf` | `(ProjectID, Equipment)` |
| `project_suppliers_1nf` | `(ProjectID, SupplierName)` |

These keys guarantee every row is unique. This satisfies the third 1NF
requirement — each row can be uniquely identified.

## Why is the design now in 1NF?

- Every cell contains exactly one value; no `|` appears anywhere.
- There are no repeating groups inside a single cell.
- Every row is uniquely identified by a primary key or composite key.

Therefore the data satisfies all three conditions of First Normal Form.

## Notes

- **Redundancy still exists in 1NF.** A worker such as `Mike Ross` appears
  multiple times across `project_workers_1nf`, `worker_skills_1nf`, and
  `worker_certifications_1nf`. This is expected: 1NF only requires atomicity,
  not removal of redundancy. Removing it is the job of 2NF, 3NF, and 4NF.

- **Partial dependency remains for the next stage.** In
  The composite key for this table is project_workers_1nf, which is made of the key (ProjectID, WorkerName).
  However, only `WorkerName` is used for WorkerPhone and WorkerHourlyRate,
  not on the whole composite key. This is a partial dependency, and
  Second Normal Form (2NF) will eliminate just that.

