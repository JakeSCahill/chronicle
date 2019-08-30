# The Tangle

**The Tangle is the immutable data structure that contains IOTA transactions. All nodes in an IOTA network store a copy of the Tangle in their ledgers, read from it, attach new transactions to it, and reach a consensus on its contents.**

Transactions in the Tangle are immutable because their contents are cryptographically referenced to the history of two other transactions. So, if any transaction were to change in that history, all the references would be broken.

:::info:
These cryptographic references are transaction hashes. Each transaction has a unique transaction hash that's derived from its contents. As a result, the contents of a transaction are immutable.
:::

The references among transactions form a type of [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph) (DAG), which is a sequence of vertices where every edge is directed from an earlier point to a later one in the sequence.

In the Tangle, vertices are transactions, and edges are references.

In this diagram, the numbered boxes represent transactions. The transactions on the left come first in the sequence, and the transactions on the right come after.

![A directed acyclic graph](../images/dag.png)

## Parents and children

When a node attaches a new transaction to the Tangle, that transaction directly references two existing ones to the left of it.

:::info:
Nodes decide which transactions to attach a new one to by doing [tip selection](root://node-software/0.1/iri/concepts/tip-selection.md).
:::

References form a family tree, whereby if a new transaction is a **child**, the transactions hashes in its [branch and trunk transaction fields](../references/structure-of-a-transaction.md) are its **parents**.

In the diagram, transaction 6 directly references transaction 5, so transaction 5 is a **parent** of transaction 6. Similarly, transaction 6 indirectly references transaction 3, so transaction 3 is a **grandparent** of transaction 6.

These direct and indirect references make up a transaction's history.

A transaction can be valid only if it references two other transaction's whose history does not conflict with it.

For example, if transaction 6 instructs the node to withdraw 10 Mi from an address, the history of its parents must lead to a point where that address is sent at least 10 Mi.

## Consensus

Nodes are responsible for validating transactions and their histories to make sure that they don't conflict. To validate a transaction, a node needs to have that transaction's history in its ledger.

Because the Tangle is distributed among all nodes in an IOTA network, some of them can have more or fewer transactions in their ledgers than other nodes. The transactions in any node's ledger make up its **view of the Tangle**.

So, to make sure that all nodes eventually have the same view of the Tangle, they forward any new transactions that they receive to their neighbors.

When a node has a transaction's entire history, the transaction is considered solid, which means that it can be considered for confirmation.

:::info:
Nodes don't need the entire history of a transaction, starting from the first ever transaction to consider it solid.

Instead, nodes need the history of a transaction up to a predefined one, which is called an entry point. When the transaction's history goes far back enough to reference an entry point, the node stops solidifying it.

An example of a predefined entry point is a [local snapshot](root://node-software/0.1/iri/concepts/local-snapshot.md).
:::

For a transaction to be considered confirmed, nodes must reach a consensus on when to consider it final before they can update the balances of addresses.

A transaction is considered confirmed when it's solid and it's directly or indirectly referenced by a transaction that's sent and signed by the Coordinator.

:::info:
This means that the transaction must be selected during tip selection when the Coordinator is creating a new milestone.
:::

### The Coordinator

The Coordinator is an application that creates, signs, and sends bundles of transactions from the same address at regular intervals. Each of these bundles contains transactions called milestones that nodes use to reach a consensus. When milestones directly or indirectly reference a transaction in the Tangle, nodes mark the state of that transaction and its entire history as confirmed.

:::info:Coordicide
At the moment, we are focused on a project called [Coordicide](https://coordicide.iota.org/), which is a proposal for the removal of the Coordinator. When this happens, nodes will be able to reach a consensus without milestones.
:::

### Milestones

The Coordinator sends milestones to nodes at regular intervals, and nodes use these milestones to reach a consensus. 

When nodes see a transaction that's been sent from the Coordinator's address, they validate it by doing the following:

* Make sure that it doesn't lead to a double-spend
* Verify its signature

Because IOTA uses the Winternitz one-time signature scheme (W-OTS), addresses must not be withdrawn from more than once. To allow the Coordinator to sign multiple bundles whose signatures can still be verified against one address, that address is derived from the Coordinator's Merkle tree.

### The Coordinator's Merkle tree

A Merkle tree is a data structure that starts by hashing data at the leaves and ends at the Merkle root (the Coordinator's address).

![Example Merkle tree](../images/merkle-tree-example.png)

The Coordinator can sign and send one signed bundle for each leaf in its Merkle tree.

In this example, we have four leaves, which each represent one of the Coordinator's public/private key pairs. These key pairs are created in advance and used to compute the the Coordinator's address. The total number of key pairs in a Merkle tree depends on its depth in this formula: 2<sup>depth</sup>. In this example, the Merkle tree's depth is 2.

:::info:
On the Mainnet, the Coordinator's Merkle tree has a depth of 23. So, the Coordinator has 8,388,608 public/private key pairs and can send the same number of milestones.
:::

To compute the Coordinator's address, the public keys are hashed in pairs:

* **Node 1:** Hash(Hash(public key of leaf 1) Hash(public key of leaf 2))
* **Node 2:** Hash(Hash(public key of leaf 3) Hash(public key of leaf 4))
* **Coordinator's address:** Hash(Hash(node 1) Hash(node 2))

Node 1 is a hash of the result of hashing both the public key of leaf 1 and the public key of leaf 2. Node 2 is a hash of the result of hashing both the public key of leaf 3 and the public key of leaf 4. The Coordinator's address is a hash of the result of hashing the hash of node 1 and node 2.

:::info:
The Coordinator's private keys are derived from a seed, an index, and a security level.

On the Mainnet, these private keys are security level 2. As a result, the milestone signature is too large to fit in one transaction and must be fragmented across two.

[Learn more about how private keys are derived](root://dev-essentials/0.1/concepts/addresses-and-signatures.md).
:::

### How nodes verify milestones

To verify milestones, nodes must rebuild the Merkle tree to find the Merkle root. If the rebuilt Merkle root is the same as the Coordinator's address, nodes know the milestone was sent by the Coordinator.

To allow nodes to rebuild the Merkle tree, the Coordinator sends the following milestones in the bundle:

* Two transactions that contain the fragmented signature
* One transaction whose [`signatureMessageFragment`](root://dev-essentials/0.1/references/structure-of-a-transaction.md) field contains enough missing data from the Merkle tree to be able to rebuild it

![Example Merkle tree](../images/merkle-tree-example.png)

For example, as a node, we have seen a bundle that was signed with the private key of leaf 1.

First, we verify the signature to find out the public key of leaf 1.

:::info:
[Learn how nodes verify signatures](root://dev-essentials/0.1/concepts/addresses-and-signatures.md#how-nodes-verify-signatures)
:::

To help us calculate the Merkle root, the third milestone in the bundle contains the following:

* The public key of leaf 2
* The hash of node 2

Now, we hash the public keys of leaves 1 and 2 to find the hash of node 1. Then we hash the hash of nodes 1 and 2 to find the Merkle root.

If the Merkle root is the same as the Coordinator's address, the bundle was signed with one of the private keys in the Coordinator's Merkle tree.

:::info:Want to run your own Coordinator?
Use Compass to create, sign, and send milestones in your own private Tangle.
:::

## Further research

We have an active research department that focuses on developing the Tangle and its related protocols.

* [Academic Papers](https://www.iota.org/research/academic-papers)
* [Roadmap](https://www.iota.org/research/roadmap)
