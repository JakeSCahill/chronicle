# Minimum weight magnitude

**Each transaction must include a valid proof of work, which is a spam prevention measure. The minimum weight magnitude (MWM) is a variable that defines how much work is done during proof of work. When you interact with an IOTA network as a client, you must use the correct MWM for that network. Otherwise, your transaction won't be valid and the nodes will reject it.**

## About proof of work

A proof of work (PoW) is a piece of data that is calculated using trial and error to meet certain requirements. The key to PoW is that it's difficult to do, but easy to check.

PoW was introduced as a concept to reduce large amounts of email spam. This concept is known as [hashcash](https://en.wikipedia.org/wiki/Hashcash), and is a method of preventing email spam by requiring a proof of work for the contents of every email.

PoW is easy to calculate for one email, but costs a lot in time and computational power to calculate for mass spam emails.

## Proof of work in IOTA

Similar to hashcash, each transaction must include a PoW before it can be validated. This PoW provides spam protection for an IOTA network by increasing the time and computational power it takes to create mass spam transactions.

To calculate the PoW for a transaction, [its contents](root://dev-essentials/0.1/references/structure-of-a-transaction.md) are converted from trytes to trits, then those trits are hashed to result in a transaction hash.

### Minimum weight magnitude

Minimum weight magnitude (MWM) defines the requirements for proof of work.

During proof of work, the transaction hash is repeatedly hashed until it ends in the same number of 0 trits as the MWM. The higher the MWM, the harder the proof of work.

:::info:
[Three 0 trits are encoded as a 9 in trytes](root://dev-essentials/0.1/references/tryte-alphabet.md).
:::

All nodes in an [IOTA network](root://getting-started/0.1/references/iota-networks.md) accept transactions whose hashes end in the same or higher number of 0 trits as their predefined MWM. If a transaction ends in fewer 0 trits than the MWM, the nodes in that network will reject it.

For example, on the Mainnet, you must use at least a MWM of 14. If you were to send a transaction whose hash ends in 9 (the MWM on the Devnet) or 7 (the MWM on the Spamnet) 0 trits, no nodes on the Mainnet would accept it.

:::info:
Every increment of the MWM triples the difficulty of the PoW.
:::

If the transaction hash ends in the correct number of 0 trits ([minimum weight magnitude](root://dev-essentials/0.1/concepts/minimum-weight-magnitude.md)), it's considered valid.

### The nonce

If the transaction hash doesn't end in the correct number of 0 trits, the value of the transaction's `nonce` field is incremented and the transaction hash is hashed again.

This process continues until a transaction hash is found that ends in the correct number of 0 trits for the MWM.

The `nonce` field of a transaction contains a string of 27 trytes that IRI nodes use to validate the PoW, for example:

```javascript
{
...
nonce: "POWSRVIO9GW99999FMGEGVMMMMM"
...
}

```

Because the the contents of the transaction are hashed, if any of the contents change, the transaction hash will change and make the proof of work invalid.

:::info:
The function that calculates PoW is called the [PearlDiver](https://github.com/iotaledger/iri/blob/fcf2d105851ee891b093e2857592fa05258ec5be/src/main/java/com/iota/iri/crypto/PearlDiver.java).
:::

### Options for doing proof of work

Clients have the following options for doing PoW:

|**Option**|**Advantages**|**Disadvantages**|
|:-------|:---------|:------------|
|Choose remote PoW on a node|Remote PoW is when clients asks a node to do PoW for a transaction. Clients do this by calling the [`attachToTangle` endpoint](root://node-software/0.1/iri/references/api-reference.md#attachToTangle). This way, clients can avoid using the computational power needed to do PoW.|Depending on how powerful the node is and how many requests it receives, it may time out and not complete the PoW |
|Do local PoW|Local PoW is when clients do PoW for each transaction that they want to attach to the Tangle. This way, clients aren't reliant on unreliable nodes to do PoW.|The client device may not be powerful enough to complete PoW in a satisfactory amount of time|
|Outsource PoW to a paid service|A paid PoW service is a third-party server that accepts IOTA transactions, completes PoW and returns it to the client. PoW is done faster more more reliably by a dedicated third-party server. An example of such a service is [powsrv.io](https://powsrv.io/#quickstart)|It costs money to use the service and you don't have control over it|
|[Install a PoW proxy server](root://node-software/0.1/iri/how-to-guides/install-a-pow-proxy.md)|PoW is done faster and more reliably by your own dedicated server|You need to maintain the PoW proxy server|
