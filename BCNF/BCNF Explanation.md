# Boyce-Codd Normal Form (BCNF) — Explanation

## Definition

A table is in BCNF if and only if, for every non-trivial functional dependency X → Y, the determinant X is a superkey (i.e., X must be a candidate key of that table). BCNF is stricter than 3NF because 3NF allows a non-key attribute to be determined by another non-key attribute as long as the non-key attribute is part of a candidate key. BCNF closes that loophole.

---

## Step 1: Functional Dependencies Checked

We systematically checked every functional dependency in each 3NF table to determine whether its determinant is a candidate key.

### BCNF_Project

| Columns | ProjectID, ProjectName, ProjectType, StartDate, EndDate, SiteAddress, SiteCity, SiteState, ClientName, SupervisorName |
|---|---|
| Candidate key | ProjectID |
| FDs checked | ProjectID → ProjectName, ProjectType, StartDate, EndDate, SiteAddress, SiteCity, SiteState, ClientName, SupervisorName |
| Verdict | **BCNF satisfied.** The only determinant is ProjectID, which is the sole candidate key. ClientName and SupervisorName are foreign key references, not independent determinants within this table. |

---

### BCNF_Client

| Columns | ClientName, ClientPhone, ClientEmail, ClientCity |
|---|---|
| Candidate keys | ClientName (primary), ClientPhone (unique per client), ClientEmail (unique per client) |
| FDs checked | ClientName → ClientPhone, ClientEmail, ClientCity |
| Verdict | **BCNF satisfied.** ClientName is the designated candidate key and is the only determinant. ClientPhone and ClientEmail are also unique identifiers (candidate keys), so no non-candidate-key attribute acts as a determinant. |

---

### BCNF_Supervisor

| Columns | SupervisorName, SupervisorPhone |
|---|---|
| Candidate keys | SupervisorName (primary), SupervisorPhone (unique per supervisor) |
| FDs checked | SupervisorName → SupervisorPhone |
| Verdict | **BCNF satisfied.** SupervisorName is a candidate key. SupervisorPhone is also unique, so it is a candidate key too. Every determinant is a candidate key. |

---

### BCNF_Worker

| Columns | WorkerName, WorkerPhone, WorkerHourlyRate |
|---|---|
| Candidate keys | WorkerName (primary), WorkerPhone (unique per worker) |
| FDs checked | WorkerName → WorkerPhone, WorkerHourlyRate |
| Verdict | **BCNF satisfied.** WorkerName is the candidate key and the only determinant. WorkerHourlyRate is an intrinsic property of the worker, not a determinant of anything else. WorkerPhone is also unique (a candidate key). |

---

### BCNF_Supplier

| Columns | SupplierName, SupplierCity |
|---|---|
| Candidate key | SupplierName |
| FDs checked | SupplierName → SupplierCity |
| Verdict | **BCNF satisfied.** SupplierName is the only determinant and is the candidate key. |

---

### BCNF_SupplierPhone

| Columns | SupplierName, SupplierPhone |
|---|---|
| Candidate keys | (SupplierName, SupplierPhone) as composite PK; SupplierPhone alone is also unique |
| FDs checked | SupplierPhone → SupplierName (each phone belongs to exactly one supplier) |
| Verdict | **BCNF satisfied.** SupplierPhone is a candidate key (it uniquely identifies a supplier), so the dependency SupplierPhone → SupplierName does not violate BCNF. |

---

### BCNF_ProjectMaterial

| Columns | ProjectID, Material, UnitCost |
|---|---|
| Candidate key | (ProjectID, Material) |
| FDs checked | Does Material → UnitCost hold? |
| Analysis | Reviewing the data: the same material (e.g., Concrete) appears with different unit costs across projects (120, 110, 130, 115, 140). UnitCost therefore depends on the full composite key (ProjectID, Material), not on Material alone. |
| Verdict | **BCNF satisfied.** There is no dependency where a non-candidate-key attribute alone determines UnitCost. The only determinant is the full composite key. |

---

### BCNF_ProjectEquipment

| Columns | ProjectID, Equipment, RentalCost |
|---|---|
| Candidate key | (ProjectID, Equipment) |
| FDs checked | Does Equipment → RentalCost hold? |
| Analysis | The same equipment (e.g., Crane) appears with different rental costs across projects (5000, 5500, 6000, 6500). RentalCost depends on the full composite key, not on Equipment alone. |
| Verdict | **BCNF satisfied.** The only determinant is the full composite key (ProjectID, Equipment). |

---

### BCNF_ProjectWorker

| Columns | ProjectID, WorkerName |
|---|---|
| Candidate key | (ProjectID, WorkerName) |
| FDs checked | None beyond the key itself (no non-key attributes). |
| Verdict | **BCNF satisfied.** A table with only key attributes and no non-key attributes trivially satisfies BCNF. |

---

### BCNF_ProjectSupplier

| Columns | ProjectID, SupplierName |
|---|---|
| Candidate key | (ProjectID, SupplierName) |
| FDs checked | None beyond the key itself (no non-key attributes). |
| Verdict | **BCNF satisfied.** Trivially in BCNF. |

---

### BCNF_WorkerSkill

| Columns | WorkerName, Skill |
|---|---|
| Candidate key | (WorkerName, Skill) |
| FDs checked | None beyond the key itself. |
| Verdict | **BCNF satisfied.** Trivially in BCNF. |

---

### BCNF_WorkerCertification

| Columns | WorkerName, Certification |
|---|---|
| Candidate key | (WorkerName, Certification) |
| FDs checked | None beyond the key itself. |
| Verdict | **BCNF satisfied.** Trivially in BCNF. |

---

## Step 2: Did Any Table Violate BCNF?

**No table violated BCNF.**

After careful analysis of all 12 tables inherited from 3NF, every functional dependency found had a candidate key as its determinant. The key findings were:

- In `BCNF_ProjectMaterial` and `BCNF_ProjectEquipment`, we carefully checked whether the material or equipment names alone determined UnitCost or RentalCost respectively. The data confirmed they do not — costs vary by project, so the full composite key is required.
- In `BCNF_SupplierPhone`, we identified that SupplierPhone is itself a candidate key, so the dependency SupplierPhone → SupplierName is acceptable under BCNF.
- All relationship tables (ProjectWorker, ProjectSupplier, WorkerSkill, WorkerCertification) have only key columns and no non-key attributes, making them trivially compliant.

---

## Step 3: Decomposition

Since no BCNF violations were found, **no decomposition was necessary**. All 12 tables from 3NF carry forward into BCNF unchanged.

---

## Summary of Candidate Keys After BCNF Check

| Table | Candidate Key(s) |
|---|---|
| BCNF_Project | ProjectID |
| BCNF_Client | ClientName, ClientPhone, ClientEmail |
| BCNF_Supervisor | SupervisorName, SupervisorPhone |
| BCNF_Worker | WorkerName, WorkerPhone |
| BCNF_Supplier | SupplierName |
| BCNF_SupplierPhone | (SupplierName, SupplierPhone), SupplierPhone |
| BCNF_ProjectMaterial | (ProjectID, Material) |
| BCNF_ProjectEquipment | (ProjectID, Equipment) |
| BCNF_ProjectWorker | (ProjectID, WorkerName) |
| BCNF_ProjectSupplier | (ProjectID, SupplierName) |
| BCNF_WorkerSkill | (WorkerName, Skill) |
| BCNF_WorkerCertification | (WorkerName, Certification) |

---

## Why the Design is in BCNF

In every table, for every non-trivial functional dependency X → Y, the left-hand side X is a candidate key of that table. This satisfies the BCNF condition exactly. Because the 3NF design already correctly isolated entities and relationships separating clients, supervisors, workers, suppliers, projects, and their relationships into distinct tables no further anomalies remained that could cause a BCNF violation. The design is lossless and dependency-preserving.
