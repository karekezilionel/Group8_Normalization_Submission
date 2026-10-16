# Fourth Normal Form (4NF) — Explanation

## Definition

A table is in 4NF if and only if it is already in BCNF and it contains no non-trivial multi-valued dependencies. A multi-valued dependency X →→ Y exists when, for a given value of X, there is a set of associated Y values that is entirely independent of any other attribute Z in the same table anf storing two independent multi-valued facts in one table causes unnecessary row combinations and update anomalies, even when no functional dependency is violated.

---

## Explanation 1: Multi-Valued Dependencies Checked

We examined every BCNF table for non-trivial multi-valued dependencies by asking: does this table record two or more independent lists of facts about the same entity?

### 4NF_Project

| Columns | ProjectID, ProjectName, ProjectType, StartDate, EndDate, SiteAddress, SiteCity, SiteState, ClientName, SupervisorName |
|---|---|
| MVDs checked | Each project has exactly one name, one type, one date range, one address, one client, and one supervisor. No multi-valued facts exist. |
| Verdict | 4NF satisfied All attributes are single-valued facts about the project and no MVD is present. |

---

### 4NF_Client

| Columns | ClientName, ClientPhone, ClientEmail, ClientCity |
|---|---|
| MVDs checked | Each client has exactly one phone, one email, and one city in this dataset and no independent lists are stored together. |
| Verdict | 4NF satisfied and no MVD is present. |

---

### 4NF_Supervisor

| Columns | SupervisorName, SupervisorPhone |
|---|---|
| MVDs checked | Each supervisor has exactly one phone number. No multi-valued facts. |
| Verdict | 4NF satisfied and no MVD is present. |

---

### 4NF_Worker

| Columns | WorkerName, WorkerPhone, WorkerHourlyRate |
|---|---|
| MVDs checked | Each worker has exactly one phone and one hourly rate. Skills and certifications were already separated into their own tables in earlier stages. |
| Verdict | 4NF satisfied and no MVD is present. |

---

### 4NF_Supplier

| Columns | SupplierName, SupplierCity |
|---|---|
| MVDs checked | Each supplier has exactly one city. Phone numbers were already separated into 4NF_SupplierPhone. |
| Verdict | 4NF satisfied and no MVD is present. |

---

### 4NF_SupplierPhone

| Columns | SupplierName, SupplierPhone |
|---|---|
| MVDs checked | SupplierName →→ SupplierPhone. A supplier may have multiple phone numbers, but this is the only multi-valued fact stored in this table. There is no second independent list mixed in. |
| Verdict | 4NF satisfied and only one multi-valued fact is recorded per supplier here. A non-trivial MVD requires two independent multi-valued facts to coexist in the same table; that condition is not met. |

---

### 4NF_ProjectMaterial

| Columns | ProjectID, Material, UnitCost |
|---|---|
| MVDs checked | ProjectID →→ Material. However, UnitCost is not independent of Material — it is a property of the (ProjectID, Material) pair, not a separate independent list. This is a functional dependency on the composite key, not a second MVD. |
| Verdict | 4NF satisfied and there is only one multi-valued fact (materials per project), and UnitCost is functionally dependent on the full key, not an independent list. |

---

### 4NF_ProjectEquipment

| Columns | ProjectID, Equipment, RentalCost |
|---|---|
| MVDs checked | ProjectID →→ Equipment. RentalCost is a property of the (ProjectID, Equipment) pair, not an independent list. |
| Verdict | 4NF satisfied and same reasoning as ProjectMaterial. Only one multi-valued fact is present. |

---

### 4NF_ProjectWorker

| Columns | ProjectID, WorkerName |
|---|---|
| MVDs checked | ProjectID →→ WorkerName. This is the only fact stored. Equipment, materials, and suppliers are each in their own separate tables, so no two independent lists are combined here. |
| Verdict | 4NF satisfied and only one multi-valued fact per table. |

---

### 4NF_ProjectSupplier

| Columns | ProjectID, SupplierName |
|---|---|
| MVDs checked | ProjectID →→ SupplierName. Only one multi-valued fact is stored. |
| Verdict | 4NF satisfied and no second independent list is present. |

---

### 4NF_WorkerSkill

| Columns | WorkerName, Skill |
|---|---|
| MVDs checked | WorkerName →→ Skill. This is the only fact stored. Certifications are in a separate table, so the two independent lists (skills and certifications) are not combined here. |
| Verdict | 4NF satisfied and separating skills and certifications into distinct tables was the key design decision that prevents a 4NF violation. |

---

### 4NF_WorkerCertification

| Columns | WorkerName, Certification |
|---|---|
| MVDs checked | WorkerName →→ Certification. Only one multi-valued fact is stored. Skills are in a separate table. |
| Verdict | 4NF satisfied and no second independent list is present. |

---

## Explanation 2: Which Multi-Valued Dependencies Were Identified?

The following non-trivial Multi-Valued Dependecies (MVDs) exist across the schema:

| Table | Multi-Valued Dependency |
|---|---|
| 4NF_WorkerSkill | WorkerName →→ Skill |
| 4NF_WorkerCertification | WorkerName →→ Certification |
| 4NF_ProjectWorker | ProjectID →→ WorkerName |
| 4NF_ProjectSupplier | ProjectID →→ SupplierName |
| 4NF_ProjectMaterial | ProjectID →→ Material (UnitCost is a dependent attribute, not a second MVD) |
| 4NF_ProjectEquipment | ProjectID →→ Equipment (RentalCost is a dependent attribute, not a second MVD) |
| 4NF_SupplierPhone | SupplierName →→ SupplierPhone |

In every case each table stores exactly one independent multi-valued fact and no table combines two independent multi-valued facts about the same entity.

---

## Explanation 3: Were Any Independent Lists Mixed Together?

**No.** The critical potential violations were:

- **WorkerName →→ Skill** and **WorkerName →→ Certification** are independent: a worker's skills have no bearing on their certifications. These were already placed in separate tables (4NF_WorkerSkill and 4NF_WorkerCertification) during earlier normalization stages. Had they been combined into a single table (WorkerName, Skill, Certification), every skill would be paired with every certification for that worker, producing spurious combinations and a clear 4NF violation.

- **ProjectID →→ WorkerName**, **ProjectID →→ SupplierName**, **ProjectID →→ Material**, and **ProjectID →→ Equipment** are all independent of each other. Each is stored in its own table. Had any two of these been combined (e.g., ProjectID, WorkerName, SupplierName in one table), every worker would be paired with every supplier for that project, which would be a 4NF violation.

Because earlier normalization stages already decomposed these into separate relationship tables, no further decomposition is needed at this stage.

---
Since no 4NF violations were found, no decomposition was necessary. All 12 tables from BCNF carry forward into 4NF unchanged.
---

## Explanation 4: Summary of Primary Keys in 4NF Tables

| Table | Primary Key | Multi-Valued Dependency |
|---|---|---|
| 4NF_Project | ProjectID | None |
| 4NF_Client | ClientName | None |
| 4NF_Supervisor | SupervisorName | None |
| 4NF_Worker | WorkerName | None |
| 4NF_Supplier | SupplierName | None |
| 4NF_SupplierPhone | (SupplierName, SupplierPhone) | SupplierName →→ SupplierPhone (only one MVD) |
| 4NF_ProjectMaterial | (ProjectID, Material) | ProjectID →→ Material (only one MVD) |
| 4NF_ProjectEquipment | (ProjectID, Equipment) | ProjectID →→ Equipment (only one MVD) |
| 4NF_ProjectWorker | (ProjectID, WorkerName) | ProjectID →→ WorkerName (only one MVD) |
| 4NF_ProjectSupplier | (ProjectID, SupplierName) | ProjectID →→ SupplierName (only one MVD) |
| 4NF_WorkerSkill | (WorkerName, Skill) | WorkerName →→ Skill (only one MVD) |
| 4NF_WorkerCertification | (WorkerName, Certification) | WorkerName →→ Certification (only one MVD) |

---

## Explanation 5: Why the Final Design is in 4NF

A table violates 4NF only when it stores two or more independent multi-valued facts about the same entity in the same table. In this design, every relationship table records exactly one multi-valued fact. The two most important separations that prevent 4NF violations are:

1. **WorkerSkill and WorkerCertification are separate tables.** Skills and certifications are independent facts about a worker. Combining them would force every skill to be paired with every certification, creating redundant and misleading rows.

2. **ProjectWorker, ProjectSupplier, ProjectMaterial, and ProjectEquipment are separate tables.** Workers, suppliers, materials, and equipment are all independent facts about a project. Combining any two of these would produce a cross-product of unrelated facts.

Because each table in the final design contains at most one non-trivial multi-valued dependency, the entire schema satisfies 4NF. The design is lossless, free of redundancy caused by independent multi-valued facts, and correctly models the real-world relationships in the construction project dataset.
