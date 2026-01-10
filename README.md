# System Design

## 1. Scalability

### 1.1 Types of Scaling

- **Vertical Scaling (Scale Up)**
  - Increase capacity of a single machine (CPU, RAM, disk).
- **Horizontal Scaling (Scale Out)**
  - Increase number of machines/instances.

### 1.2 Infrastructure Options

- **On-premise servers**
- **Cloud providers**
  - AWS
  - GCP
  - Azure
- **Serverless**
  - Execute code without managing servers (e.g., AWS Lambda)

---

## 2. Database Scaling and Failover

### 2.1 Cold Standby

- Periodic backups are taken from the primary database and stored on disk.
- In case of failure:
  - A new standby server is created.
  - Data is restored from the latest backup.
- **Pros:** Simple and low cost.
- **Cons:** High downtime and possible data loss since last backup.

---

### 2.2 Warm Standby

- Secondary database continuously syncs with primary using replication.
- In case of primary failure:
  - Traffic is switched to secondary within seconds.
- **Pros:** Low downtime.
- **Cons:** Secondary usually handles no traffic until failover.

---

### 2.3 Hot Standby

- Standby database is synced in near real-time.
- Standby also serves **read traffic**.
- In case of primary failure:
  - Standby can immediately start handling writes.
  - A new standby can be added.
- **Pros:** Very low downtime, better resource utilization.
- **Cons:** More complex and higher cost.

---

### 2.4 Multi-Primary (Multi-Master)

- Multiple databases can read and write simultaneously.
- Traffic is distributed across multiple primaries.
- **Pros:** High availability and horizontal write scaling.
- **Cons:** Data conflict resolution is complex.

---
