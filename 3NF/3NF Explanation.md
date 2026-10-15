# Third Normal Form (3NF) — Explanation

I reviewed the 2NF project table for attributes that depended on other non-key attributes. I found two transitive dependencies:

- `ProjectID → ClientName → ClientPhone, ClientEmail, ClientCity`
- `ProjectID → SupervisorName → SupervisorPhone`

For example, the client contact details are determined by the client, not by each individual project. The supervisor’s phone number is determined by the supervisor’s name.

## Changes made

I created `3NF_Client.csv` to store each client’s phone, email, and city once, and `3NF_Supervisor.csv` to store each supervisor’s phone once. I removed those details from `3NF_Project.csv`, leaving `ClientName` and `SupervisorName` there as references to the new tables.

I carried forward the other tables from 2NF because their entity and relationship details were already separated, and I found no additional transitive dependencies in them.

## Keys and relationships

- `3NF_Project.csv` uses `ProjectID` as its primary key. `ClientName` refers to `3NF_Client.csv`, and `SupervisorName` refers to `3NF_Supervisor.csv`.
- `3NF_Client.csv` uses `ClientName` as its primary key.
- `3NF_Supervisor.csv` uses `SupervisorName` as its primary key.
- The remaining tables keep the primary and foreign keys described in the 2NF stage.

## Why the design is in 3NF

Client contact details now depend on the client name, and the supervisor phone depends on the supervisor name. Project details depend on `ProjectID`. This removes the transitive dependencies from the project table while retaining its links to the client and supervisor tables.
