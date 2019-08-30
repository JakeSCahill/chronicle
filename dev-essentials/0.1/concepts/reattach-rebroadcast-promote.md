# Reattach, rebroadcast, and promote

**A bundle may be pending for many reasons such as an increased load on the network. To increase the chances of a bundle becoming confirmed, you can reattach, rebroadcast, or promote it**

## Reattach

Your transaction may have been attached to a part of the Tangle that is no longer considered valid. As a result, that transaction will never be included in the consensus.

To reattach a bundle means to create a new one and attach it to a different part of the Tangle. This way, you give your transaction another chance at being included in the consensus.

When you reattach a bundle, you keep most of the original bundle's fields the same, but you request new tip transactions (the new part of the Tangle), and do the proof of work again. As a result, the only fields that change are the following:

* [`hash`](../references/structure-of-a-transaction.md)
* [`trunkTransaction`](../references/structure-of-a-transaction.md)
* [`branchTransaction`](../references/structure-of-a-transaction.md)
* [`attachmentTimestamp`](../references/structure-of-a-transaction.md)
* [`nonce`](../references/structure-of-a-transaction.md)

You may want to reattach a bundle if its transactions have been pending for more than ten minutes. After this time, the tail transaction in the pending bundle is unlikely to be selected during tip selection, which makes it unlikely to be included in the consensus.

When you reattach a bundle that transfers IOTA tokens, only one will ever be confirmed. The others will remain pending because they will lead to double-spends (spending the same IOTA tokens twice).

## Rebroadcast

While your transactions are being sent to a node, it may go offline. In this case, the node may not forward your transactions to its neighbors. As a result, the rest of the network won't ever see your transactions, and they won't be included in the consensus.

To rebroadcast a bundle means to send the same bundle to a node again. This way, you give your transactions another chance at being forwarded to the rest of the network.

## Promote

To promote a bundle means to increases its chances of being selected during tip selection by increasing the [cumulative weight](root://node-software/0.1/iri/concepts/tip-selection.md) of its tail transaction. When you promote a bundle, you create and send a zero-value transaction that references both its tail transaction and the latest milestone.

Promoting a bundle is often more effective than reattaching a bundle, unless the bundle you're promoting leads to an inconsistent state (double-spend) or is older than the last six milestones.