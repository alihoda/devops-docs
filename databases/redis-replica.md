# Redis Replication

The *leader follower (master-replica)* format.
Replication mechanisms:

1. Master-replica connection is healthy, then master keeps sending a stream of commands to replicas to replicate the effects happening on the dataset.
2. When the connection breaks, the replica try to reconnect and attempt to proceed a partial resynchronization.
3. If partial resynchronization is not possible, the master sends a full resynchronization which needs to take a snapshot of all data.
