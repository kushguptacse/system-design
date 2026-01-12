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

## 3. Database Sharding (true horizontal scaling)
- All above approaches is kind of vertical scaling as data resides in single instance. Via sharding data will be stored in multiple places and distributed according to shard-key. and each shard will have it's own backup.
- Combining data on basis of multiple shards is complicated, so design shards accordingly that single shard would be good enough to answer query. e.g. shard key: users with id 1000-5000 empID.
### 3.1 MongoDB Architecture
#### 3.1.1 Shard
A MongoDB instance or replica set that stores a subset of data.
#### 3.1.2 Config Servers
Store cluster metadata and chunk mapping.
#### 3.1.3 mongos (Query Router)
Routes client queries to appropriate shards.
#### 3.1.4 Replica Set Structure
Primary: Handles all writes
Secondary: Replicates data from primary
Arbiter (optional): Participates in elections, stores no data
Downside is that nodes are sitting idle with only primary handling write traffic and with readConfigured secondary can provide read feature. but by this system is partition tolerant and consitant as only primary handle write and read by default. and less available as if primary goes down it will take few seconds to elect new leader.
### 3.2 Cassandra  architecture
Masterless (peer-to-peer) Archictecture where nodes are part of rings structure.
All nodes are equal and can accept reads/writes.
It provide eventual consitency as all node can read write so data written in 1 node. but it is high available and partition tolerant.

### 3.3 Celebrity Problem
Let say userId is taken as shard key and there is a users who has more fanbase and hence more queries. so in this case even with this shard key system might fail for that celeb. So, to To Solve such scenerio sharding can be done by further division of data like celebId#1 + celebId#2 + ..... + celebId#N on different node and cache celeb data aggressively on basis of hit count.

### 3.4 Data DeNormalization
In traditional relational database data is saved in multiple tables with id used as primary and foreign key. This is called normalized data. 
It takes less spaces as data is not duplicated, update in only one table. it requires join to fetch data.
In De-Normlized table data is stored in single place instead of two. this make read faster and easy. updates are hard and more storage space required.
