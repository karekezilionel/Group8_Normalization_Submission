## 2NF Explanation

After converting the raw data to 1NF, we identified tables with composite primary keys and checked for partial dependencies — cases where a non-key column depends on only part of the composite key.

### Partial dependencies found:

1. project_workers_1nf — Composite key (ProjectID, WorkerName). WorkerPhone and WorkerHourlyRate depended only on WorkerName. We moved these to a new Worker table with WorkerName as PK.

2. worker_skills_1nf — Composite key (ProjectID, WorkerName, Skill). Skill depends only on WorkerName, not on ProjectID. We removed ProjectID and created WorkerSkill with PK (WorkerName, Skill).

3. worker_certifications_1nf — Same issue. Created WorkerCertification with PK (WorkerName, Certification).

4. supplier_phones_1nf — Composite key (SupplierName, SupplierPhone). SupplierCity depended only on SupplierName. We created Supplier with PK SupplierName and kept SupplierPhone as a separate table.

### Tables already in 2NF:

1. projects_1nf — single-column PK ProjectID, so no partial dependency possible.

2. project_materials_1nf — UnitCost varies by project (Concrete costs 120 for P001 but 110 for P002), so it depends on the whole key (ProjectID, Material).

3. project_equipment_1nf — RentalCost varies by project (Crane costs 5000–6500 across projects), so it depends on the whole key (ProjectID, Equipment).

4. project_suppliers_1nf — all columns are key attributes.

### Foreign keys introduced: 
2NF_ProjectWorker, 2NF_WorkerSkill, 2NF_WorkerCertification, 2NF_SupplierPhone, 2NF_ProjectSupplier, 2NF_ProjectMaterial, and 2NF_ProjectEquipment all use foreign keys to reference their parent tables.

Every non-key column now depends on the whole primary key of its table.