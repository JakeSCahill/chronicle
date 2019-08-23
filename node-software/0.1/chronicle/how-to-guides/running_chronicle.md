# Running Chronicle

## Prerequisites

Before you start running Chronicle, you need to consider the following:

* **The expected size of your permanode:** Depending on the length of time you expect to run your permanode, decide where to host the apps and the database 

* **The location of your permanode:** Chronicle and ScyllaDB should be located in the same datacenter but run on different nodes.

* **The amount of data transmission:** A Jumbo MTU with a maximum transmission unit of 9000 bytes is recommended between ScyllaDB and Chronicle.  

* **Power outages:** Failure occurs with a full power outage. Computers running Chronicle and ScyllaDB should have a backup power supply and Internet connections. A power outage for multiple nodes will not affect data consistency as long as you have at least one active node to write the same queries to the database.

## Step 1. Install Elixir

You can install the Elixir Docker container or install Elixir native

ELIXIR DOCKER

Follow the instructions at: https://elixir-lang.org/install.html#docker

To run Elixir, enter the interactive mode

docker run -it --rm elixir

Enter bash within the container where elixir was installed:

docker run -it --rm elixir bash

TODO:  Connect Elixir to ScyllaDB and install Chronicle

ELIXIR NATIVE

For this example, we used the Ubuntu 18.04 operating system. You can also use Ubuntu versions 14.04, 16.04, 17.04, 18.04, or 19.04 or Debian versions 7, 8, or 9.

1. Add the Erlang Solutions repo

    ```bash
    wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb && sudo dpkg -i erlang-solutions_1.0_all.deb && sudo apt-get update
    ```

2. Install the Erlang/OTP platform and all its applications

    ```bash
    sudo apt-get install esl-erlang
    ```

3. Install Elixir

    ```bash
    sudo apt-get install elixir

    export PATH="$PATH:/path/to/elixir/bin"

    /usr/bin/elixir
    /usr/lib/elixir
    ```

TODO:  Connect Elixir to ScyllaDB and install Chronicle

## Step 2. Install ScyllaDB

SCYLLADB
Download the ScyllaDB from:

 https://www.scylladb.com/download/open-source/#docker

Get the ScyllaDB IP address and save it.  The default address is 172.17.0.4

Follow these instructions to run ScyllaDB:

https://docs.scylladb.com/operating-scylla/manager/1.3/run-in-docker/

https://docs.scylladb.com/operating-scylla/procedures/tips/best_practices_scylla_on_docker/#starting-a-single-scylla-node

## Step 3. Install Chronicle

Copy Chronicle
git clone https://github.com/iotaledger/chronicle.git

cd chronicle
mix compile
iex -S mix

Follow these instructions to edit the config file of Chronicle's core app to match your Scylla database's IP address:

https://github.com/iotaledger/chronicle/blob/master/apps/core/config/config.exs

When Elixir Docker boots, it should init the required tangle keyspace and tables by the Core app. The Broker(WIP) app will handle the importing process and the current ZMQ flow, finally the API app is a service app which handle the incoming APIs calls.

## Run Chronicle

The default node setup for Chronicle is a single node setup. A multiple node setup is allowed.

# Manage data in Chronicle
	
The [nodetool utility](https://docs.scylladb.com/operating-scylla/nodetool/) provides a simple command-line interface for managing data. Scylla’s nodetool is a fork of the Apache Cassandra nodetool with the same syntax and a subset of the operations.