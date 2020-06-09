---
title: Disaster Recovery
summary: Learn how about CockrachDB disaster recovery capabilities and what to do if you encounter an issue.
toc: true
---

While CockroachDB is built to be fault-tolerant and to recover automatically, sometimes disasters happen. A disaster is any event that puts your cluster at risk, and usually means you are experiencing hardware failure or data corruption. Having a disaster recovery plan enables you to recover quickly, while limiting the consequences.

### Hardware failure survivability

When planning to survive through hardware failures, these are the replication factors to apply in order to have greater resilience.  Do note that increasing data replication factors may have an impact on transaction performance since quorum levels are increasing.

[INSERT TABLE ABOUT SURVIVABILITY]

### What to do when hardware fails

[INSERT TABLE ABOUT HARDWARE FAILURES]

## Data corruption

Data corruption can occur when x, y, z.



### How to recover from corrupted data in a table

### How to recover from corrupted datea in a database

### How to recover from compromised security keys
