# Configure Replication

## Requirements

1. At least two nodes. One for *Primary* and another for *Replica*.
2. Install mariadb: `apt-get install mariadb-server -y`

## Primary Node

1. Configure hostname and enable binary-log

    ```cnf
    # $MARIADB_HOME/my.cnf

    [mariadb]
    log-bin
    server_id=1
    log_basename=primary1
    binlog-format=mixed
    ```

    > Binary log formats: mixed, raw, and statement.

2. Restart Mariadb service: `systemctl restart mariadb`

3. Create a replica user and grant slave permissions

    ```txt
    CREATE USER 'replication_user'@'%' IDENTIFIED BY 'bigs3cret';
    GRANT REPLICATION SLAVE ON *.* TO 'replication_user'@'%';
    ```

4. Get the primary's' binary log co-ordinate

    ```txt
    FLUSH TABLES WITH READ LOCK;
    SHOW MASTER STATUS;
    UNLOCK TABLES;
    ```

## Replica Node

1. Configure host and enable binary-log

    ```cnf
    [mariadb]
    server-id              = 2
    log_bin                = /var/log/mysql/mysql-bin.log
    max_binlog_size        = 100M
    relay_log = /var/log/mysql/mysql-relay-bin
    relay_log_index = /var/log/mysql/mysql-relay-bin.index
    ```

2. Restart Mariadb service: `systemctl restart mariadb`

3. Setup replica to replicate from the primary

    ```mariadb
    STOP SLAVE;

    CHANGE MASTER TO
    MASTER_HOST='master.domain.com',
    MASTER_USER='replication_user',
    MASTER_PASSWORD='bigs3cret',
    MASTER_PORT=3306,
    MASTER_LOG_FILE='master1-bin.000096',
    MASTER_LOG_POS=568,
    MASTER_CONNECT_RETRY=10;

    START SLAVE;
    SHOW SLAVE STATUS \G;   //Slave_IO_Running and Slave_SQL_Running
    ```

> Recommend to use GUID as well: `CHANGE MASTER TO MASTER_USE_GTID = slave_pos`.
