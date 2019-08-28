# Chronicle

**Chronicle is a type of node software that allows you to store all transactions that reach an [IRI node](root://node-software/0.1/iri/introduction/overview) in a distributed database that scales well**

Over time, the ledger of an IRI node accumulates many transactions, which often cause it to become larger than the node's available memory. To stop the ledger from becoming too large, these nodes often take local snapshots that prune transactions. If you need to keep all the transactions without pruning any, Chronicle can store them in a distributed Scylla database.

## Storing transactions using Chronicle
Using [ZMQ](root://node-software/0.1/iri/references/zmq-events.md), Chronicle listens to IRI nodes.  When Chronicle receives a new transaction, it processes it through the umbrella project, then stores it in the Scylla database.
