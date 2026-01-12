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

## 3. Database Sharding (True Horizontal Scaling)

- All above approaches are kind of **vertical scaling**, as data resides in a single instance.
- Via **sharding**, data is stored in multiple places and distributed according to a **shard key**.
- Each shard has its **own backup**.
- Combining data across multiple shards is complicated, so shards should be designed in a way that a **single shard can answer most queries**.
  - Example shard key: users with `empId` range `1000–5000`

---

### 3.1 MongoDB Architecture

#### 3.1.1 Shard
- A MongoDB instance or replica set that stores a subset of data.

#### 3.1.2 Config Servers
- Store cluster metadata and chunk mapping.

#### 3.1.3 mongos (Query Router)
- Routes client queries to the appropriate shards.

#### 3.1.4 Replica Set Structure
- **Primary**: Handles all writes  
- **Secondary**: Replicates data from the primary  
- **Arbiter (optional)**: Participates in elections, stores no data  

**Downside:**
- Only the primary handles write traffic.
- By default, reads also go to the primary (secondaries can serve reads if configured).
- System is **Partition Tolerant** and **Consistent** since only the primary handles writes.
- Availability is lower because if the primary goes down, a few seconds are required to elect a new leader.

---

### 3.2 Cassandra Architecture

- **Masterless (peer-to-peer)** architecture.
- Nodes are part of a **ring structure**.
- All nodes are equal and can accept **reads and writes**.
- Provides **eventual consistency**, since data can be written to any node.
- Highly **Available** and **Partition Tolerant**.

---

### 3.3 Celebrity Problem

- Suppose `userId` is used as the shard key.
- A celebrity user with a large fanbase generates a very high number of queries.
- Even with sharding, a single shard may become overloaded.

**Solution:**
- Further divide data using composite keys like:  
  `celebId#1`, `celebId#2`, … `celebId#N`
- Distribute these across different nodes.
- Cache celebrity data aggressively based on hit count.

---

### 3.4 Data De-normalization

- In traditional relational databases, data is stored in multiple tables using **primary and foreign keys**.
- This is called **normalized data**.
- Advantages:
  - Less storage (no duplication)
  - Updates happen in one place
- Disadvantages:
  - Requires joins for reads

**De-normalized Data:**
- Data is stored in a single place instead of multiple tables.
- Advantages:
  - Faster and simpler reads
- Disadvantages:
  - More storage required
  - Updates are harder and error-prone


