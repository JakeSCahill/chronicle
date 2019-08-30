# Bundles and transactions

**If you want to send anything to an IOTA network, you must send it to a node in the form of a transaction. A transaction is a single operation that can be either an input (withdraw from an address) or an output (deposit into an address or have zero-value). To send a node one or more transactions, you must group them in a bundle.**

Each transaction in a bundle has the following structure that allows nodes to validate them and attach them to the Tangle.


| **Field**                         | **Description**                                                                                   |
| :----------------------------- | :------ |
| `signatureMessageFragment`      | A signature or a message, both of which may be _fragmented_ over multiple transactions in the bundle. This field contains a signature if the transaction withdraws IOTA tokens. Otherwise, this field can contain a tryte-encoded message or all 9's where no message is defined. |
| `address`                       | Contains either the sender or recipient's address. This field contains a recipient's address if the transaction is an output. Otherwise, this field contains an address from which IOTA tokens are being withdrawn (transaction with a negative `value` field).   |
| `value`                    | Amount of IOTA tokens to deposit to or withdraw from the address | 
| `obsoleteTag`                   | User-defined tag |
| `timestamp`                     | Unix epoch: Seconds since Jan 1, 1970 (not-enforced and can be arbitrary)                                                                                                                                                                                    |
| `currentIndex`                  | Index of the current transaction in the bundle                                                                                                                                                                                                   |
| `lastIndex`                     |Index of the last transaction in the bundle                                                                                                                                                                                           |
| `bundle`                        | Hash of the bundle                |
| `trunkTransaction`              |Transaction hash of a [parent transaction](../concepts/the-tangle.md#parent-and-children). This transaction hash can either be of an existing transaction in the Tangle or of [the next transaction index in the same bundle](../references/structure-of-a-bundle.md).                                                                                                                                 |
| `branchTransaction`             |Transaction hash of a [parent transaction](../concepts/the-tangle.md#parents-and-children)                                                                                                                                                                 |
| `attachmentTag`                |User-defined tag                                                                                                                                                                                                              |
| `attachmentTimestamp`          |Unix epoch: Milliseconds since Jan 1, 1970 (after POW)                                                                                                                                                                                                           |
| `attachmentTimestampLowerBound` |Lower limit of the `attachmentTimestamp` field (not currently used)                                                                                                                                                                                                      |
| `attachmentTimestampUpperBound` |Upper limit of the `attachmentTimestamp` field (not currently used)                                                                                                                                                                                                         |
| `nonce`                         |Trytes that represent the amount of times a transaction must be hashed to check the [proof of work](root://dev-essentials/0.1/concepts/minimum-weight-magnitude.md).                                      |


When a transaction is grouped in a bundle, it's given both a `currentIndex` field, which defines its place in the bundle, and a `lastIndex` field, which defines the last transaction in a bundle.

:::info:
The structure of a bundle consists of a head, a body, and a tail, where the tail is first (index 0) and the head is the last transaction in the bundle.
:::

All transactions in the same bundle have the same value in the `bundle` field. This field contains the bundle hash, which is derived from the **bundle essence**, which consists of a hash of the values of the following transaction fields:

* `address`
* `value`
* `obsoleteTag`
* `currentIndex`
* `lastIndex`
* `timestamp`

So, if the values of any of these transaction fields were to change, the nodes would invalidate all transactions in the bundle.

As a result, bundles are atomic: Either all transactions in the bundle are valid and confirmed or none of them are.

To explain why bundles need to be atomic, take this example.

> You're at an online checkout and the total to pay is 10 Mi. Your seed has 2 addresses (index 0 and 1), which both contain 5 Mi. So, you create three transactions: One input transaction to withdraw 5 Mi from address 0, another input transaction to withdraw 5 Mi from address 1, and one output transaction to deposit 10 Mi to the vendor's address. (We'll assume that both addresses in the input transactions were created from a private key with security level 1, so the signatures can fit in each transaction.)

> For the vendor to receive 10 Mi, all three of those transactions must be valid. They're sequential instructions that rely on each other's validity to achieve the goal of transferring 10 Mi.

:::info:
It's not just multiple transactions that need to be packaged in a bundle, even individual ones do. In this case, the single transaction would be the head and tail transaction in the bundle. 
:::

## Types of transaction

Transactions can be one of two types:

* Input transaction
* Output transaction

### Input transactions

Input transactions withdraw IOTA tokens from addresses.

Bundles can contain multiple input transactions, and each one must include a valid [signature](../concepts/addresses-and-signatures.md). The length of the signature depends on the [security level](../references/security-levels.md) of the address. If the security level of the address is greater than 1, the signature is too large to fit in one transaction and must be fragmented across zero-value output transactions.

:::danger:
[Addresses must not be withdrawn from more than once](../concepts/addresses-and-signatures.md#address-reuse). As a result, input transactions must withdraw all IOTA tokens from an address even if the sender does not want to transfer all of them to the recipient. The remaining IOTA tokens can be deposited into a remainder address (usually the sender's address) in an output transaction.
:::

### Output transactions

Output transactions can be one of the following:

* A zero-value transactions that contains only a message in the `signatureMessageFragment` field
* A transaction with a positive value that deposits IOTA tokens into an address

Bundles can contain multiple output transactions. If a message in an output transaction is larger than the `signatureMessageFragment` field, the message can be fragmented across other zero-value output transactions.

:::info:
Transactions that deposit IOTA tokens can also contain a message because they don't withdraw IOTA tokens, and therefore don't contain a signature.
:::

## Options for sending a bundle

When you've created your bundle, you need to send it to a node along with two other arguments:

* Depth
* Minimum weight magnitude

The depth affects how far back in the Tangle the node starts the [tip selection algorithm](../concepts/the-tangle.md#tip-selection). Nodes use this algorithm to find two existing tail transactions in the Tangle that a new transaction can reference in the `branchTransaction` and `trunkTransaction` fields.

:::info:
The greater the depth, the farther back in the Tangle the node starts, so a greater depth makes tip selection take longer and makes the node use more computational power.
:::

The [minimum weight magnitude](../concepts/minimum-weight-magnitude.md) (MWM) is a variable that defines how much work is done during proof of work. When you send a transaction to a node, you must use the correct MWM for that node's network. Otherwise, your transaction won't be valid and all nodes will reject it.

For example, the MWM on the Mainnet is 14, but the MWM on the Devnet is only 9.

## References among transactions and bundles

Each transaction in a bundle, except the head, [references the proceeding one](../references/structure-of-a-bundle.md) through the `trunkTransaction` field. These connections allow nodes to reconstruct bundles in the Tangle and validate the contents of all its transactions.

The other `branchTransaction` and `trunkTransaction` fields reference the tail transactions of two existing bundles in the Tangle.

:::info:
[Send a bundle of transactions](../how-to-guides/send-bundle.md) to see these references.
:::

## How nodes validate bundles

After you send a bundle to a [node](root://node-software/0.1/iri/introduction/overview.md), it validates the transactions and appends each one to its ledger.

During [tip selection](root://node-software/0.1/iri/concepts/tip-selection.md), a node finds and [validates each transaction in your bundle](root://node-software/0.1/iri/concepts/transaction-validation.md#bundle-validator) by traversing its `trunkTransaction` field.

When the node has validated all transactions up to the head, your bundle is considered valid.

![Example of a bundle of 4 transactions](../images/bundle.png)

## Example bundles

### Withdraw from an address with security level 1

This bundle transfers 80 i to a recipient from an address with a security level of 1.

| **Transaction index** | **Transaction contents**                                                     | **Transaction value**                                          |
| ----- | ------------------------------------------------------------------------- | --------------------------------------------------------------- |
| 0     | Recipient's address                       | 80 (sent to the recipient's address)                    |
| 1     | Sender's address and its signature | -100 (the total balance of the sender's address as a negative value) |
| 2    | An address for the transfer of the remaining IOTA tokens (usually one of the sender's addresses)                      | 20 (remainder of the sender's address in transaction 1)                          |

### Withdraw from an address with security level 2

This bundle transfers 80 i to a recipient from an address with a security level of 2.

| **Transaction index** | **Transaction contents**                                                     | **Transaction value**                                          |
| ----- | ------------------------------------------------------------------------- | --------------------------------------------------------------- |
| 0     | Recipient's address                       | 80 (sent to the recipient's address)                    |
| 1     | Sender's address and the first part of its signature | -100 (the total balance of the sender's address as a negative value) |
| 2     | Sender's address and the rest of its signature                                        | 0                                                               |
| 3     | An address for the transfer of the remaining IOTA tokens (usually one of the sender's addresses)                      | 20 (remainder of the sender's address in transaction 1)                          |

### Withdraw from an address with security level 3

This bundle transfers 80 i to a recipient from an address with a security level of 3.

| **Transaction index** | **Transaction contents**                                                     | **Transaction value**                                          |
| ----- | ------------------------------------------------------------------------- | --------------------------------------------------------------- |
| 0     | Recipient's address                       | 80 (sent to the recipient's address)                    |
| 1     | Sender's address and the first part of its signature | -100 (the total balance of the sender's address as a negative value) |
| 2     | Sender's address and the second part of its signature                                         | 0                                                               |
| 3    | Sender's address and the rest of its signature                                         | 0                                                               |
| 4     | An address for the transfer of the remaining IOTA tokens (usually one of the sender's addresses)                             | 20 (remainder of the sender's address in transaction 1)                          |

