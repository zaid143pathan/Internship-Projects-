# Galera Cluster Setup

This guide explains how to configure a 3-node Active-Active MariaDB Galera Cluster on Ubuntu Server.

---

# Prerequisites

- Ubuntu Server 22.04
- 3 Virtual Machines
- MariaDB Installed
- Network connectivity between all nodes

---

# Node Information

| Node | Private IP |
|------|-------------|
| DB_Node1 | 192.168.0.5 |
| DB_Node2 | 192.168.0.6 |
| DB_Node3 | 192.168.0.4 |

---

# Step 1 — Update System
Run on all nodes:
bash
sudo apt update && sudo apt upgrade -y

# Step 2 — Install MariaDB and Galera
Run on all nodes:
sudo apt install mariadb-server mariadb-client galera-4 rsync -y

# Step 3 — Configure Galera
Open configuration file:
sudo nano /etc/mysql/mariadb.conf.d/60-galera.cnf
DB_Node1 Configuration
[galera]

wsrep_on=ON

wsrep_provider=/usr/lib/galera/libgalera_smm.so

wsrep_cluster_name="zaid_cluster"

wsrep_cluster_address="gcomm://192.168.0.5,192.168.0.6,192.168.0.7"

binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2

wsrep_node_address="192.168.0.5"
wsrep_node_name="DB_Node1"

wsrep_sst_method=rsync


DB_Node2 Configuration
[galera]

wsrep_on=ON

wsrep_provider=/usr/lib/galera/libgalera_smm.so

wsrep_cluster_name="zaid_cluster"

wsrep_cluster_address="gcomm://192.168.0.5,192.168.0.6,192.168.0.7"

binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2

wsrep_node_address="192.168.0.6"
wsrep_node_name="DB_Node2"

wsrep_sst_method=rsync


DB_Node3 Configuration
[galera]

wsrep_on=ON

wsrep_provider=/usr/lib/galera/libgalera_smm.so

wsrep_cluster_name="zaid_cluster"

wsrep_cluster_address="gcomm://192.168.0.5,192.168.0.6,192.168.0.7"

binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2

wsrep_node_address="192.168.0.7"
wsrep_node_name="DB_Node3"

wsrep_sst_method=rsync

# Step 4 — Stop MariaDB Service
Run on all nodes:
sudo systemctl stop mariadb

# Step 5 — Bootstrap Cluster
Run ONLY on DB_Node1:
sudo galera_new_cluster

# Step 6 — Start Remaining Nodes
Run on DB_Node2:
sudo systemctl start mariadb
Run on DB_Node3:
sudo systemctl start mariadb

# Step 7 — Verify Cluster
Login to MariaDB:
sudo mysql
Run:
SHOW STATUS LIKE 'wsrep_cluster_size';
Expected Output:
3

# Step 8 — Create Database and Table
CREATE DATABASE company;

USE company;

CREATE TABLE employees(
id INT PRIMARY KEY,
name VARCHAR(50)
);

# Step 9 — Insert Data
INSERT INTO employees VALUES(1,'Zaid');
Step 10 — Verify Replication

On another node:

USE company;

SELECT * FROM employees;

Expected Output:

1 | Zaid
Step 11 — Active-Active Replication Test

Insert data from another node:

INSERT INTO employees VALUES(2,'Harsh');

Verify on remaining node:

SELECT * FROM employees;

Expected Output:

1 | Zaid
2 | Harsh
Cluster Verification Commands
Check Cluster Size
SHOW STATUS LIKE 'wsrep_cluster_size';
Check Sync State
SHOW STATUS LIKE 'wsrep_local_state_comment';

Expected Output:

Synced


# Ports Used
Port	Purpose
3306	MariaDB
4567	Galera Replication
4568	IST
4444	SST


# Conclusion

This project successfully demonstrates:

Active-Active replication
Real-time synchronization
Automatic node recovery
High availability database architecture
Fault-tolerant MariaDB Galera Cluster
