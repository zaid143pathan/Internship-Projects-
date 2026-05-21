# 3-Node Galera Cluster Setup

## Project Overview

This project demonstrates the setup of a **3-node Active-Active MariaDB Galera Cluster** using Ubuntu Server.  
The cluster provides:

- Real-time synchronous replication
- High availability
- Fault tolerance
- Automatic node synchronization

Any changes made on one database node are automatically reflected on all other nodes.

---

# Architecture


                ┌─────────────────────┐
                │     Galera Cluster  │
                │ Active-Active Setup │
                └──────────┬──────────┘
                           │
     ┌─────────────────────┼─────────────────────┐
     │                     │                     │
┌────▼────┐          ┌─────▼────┐          ┌────▼────┐
│DB_Node1 │◄────────►│DB_Node2  │◄────────►│DB_Node3 │
│192.168.0.5         │192.168.0.6         │192.168.0.4|
└─────────┘          └──────────┘          └─────────┘

     Real-Time Synchronous Replication
