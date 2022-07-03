# Common Issues in Databases

## Replication Lag

*A delay data being copied to a secondary server after an update on the primary server* or in other words, *amount of time that is takes to copy (i.e. replicate) a write operation on the primary to a secondary*.

### Anomalies Caused by Replication Lag

1. Being able to read your own read
2. Moving back in time
3. Violation of causality

More info at <https://www.brainstobytes.com/db-replication-iv-replication-lag/>.
