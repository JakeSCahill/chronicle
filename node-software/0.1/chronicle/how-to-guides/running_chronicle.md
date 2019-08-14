# Running Chronicle

#### Location
Chronicle and ScyllaDB should be located in the same datacenter but run on different nodes. There are future plans to support running across multiple datacenters.

#### Data transmission
A Jumbo MTU with a maximum transmission unit of 9000 bytes is recommended between ScyllaDB and Chronicle.  

#### Default setup
The default node setup for Chronicle is a single node setup. A multiple node setup is allowed.  

#### Power outage
Failure occurs with a full power outage. Computers running Chronicle and ScyllaDB should have a backup power supply and Internet connections. A power outage for multiple nodes will not affect data consistency as long as you have at least one active node to write the same queries to the database.

:::info:
Node swarms are still being developed.
:::

# Managing data
	
The [nodetool utility](https://docs.scylladb.com/operating-scylla/nodetool/) provides a simple command-line interface for managing data. Scylla’s nodetool is a fork of the Apache Cassandra nodetool with the same syntax and a subset of the operations.
