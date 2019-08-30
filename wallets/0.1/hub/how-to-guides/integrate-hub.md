# Integrate Hub into a cryptocurrency exchange

**You can integrate Hub into an exchange in many ways, depending on how you want to manage your users' balances.**

By default, Hub keeps a record of all user balances in a database, and allows users to trade with each other through their Hub accounts.

In this guide, we discuss two integration options:

- **Manage user balances in Hub (default):** Easiest way to manage trades and user balances
- **Manage user balances outside of Hub:** Easiest way to store all users' IOTA tokens outside of Hub

## Integration option 1. Manage user balances in Hub

Hub supports user accounts that each have a tracked balance. This way,users can trade and withdraw only as many tokens as they own.

To action a trade, you can use the `ProcessTransfers` or the `UserWithdraw` endpoints.

:::info:
When you use the `ProcessTransfers` endpoint, no tokens are transferred on the Tangle. Instead, the users' balances are updated in the database, which affects how many tokens users can request to withdraw.

When you use the `UserWithdraw` endpoint, the tokens are transferred on the Tangle and the users' balances are updated in the database.
:::

Because the `UserWithdraw` endpoint allows you to withdraw only from a user address and not the Hub owner's addresses, it's difficult to store tokens outside of Hub (in a cold wallet).

|**User action**|**Exchange action**|**Hub endpoint**|
|:----------|:--------------|:-----------|
|Signs up for an IOTA account on the exchange|Creates a new user in Hub| `CreateUser`|
|Requests a deposit address in which to deposit IOTA tokens|Creates a new deposit address for the user|`GetDepositAddress`|
|Deposits IOTA tokens into the address|Notifies the user on the exchange frontend after the deposit has been swept to one of the Hub owner's new addresses| `BalanceSubscription`|
|Requests a withdrawal to an address outside of Hub|Actions the withdrawal|`UserWithdraw`|
|Buys tokens from another user|Actions the trade between the two users by updating their balances in the Hub database|`ProcessTransfers`|

### Store IOTA tokens outside of Hub

To store IOTA tokens outside of Hub, you need to transfer them from one of the Hub owner's addresses to the new cold wallet address.

:::warning:Warning
When you transfer tokens outside of Hub, you're at risk of not being able to process withdrawal requests.
:::

1. Stop Hub

2. Set the [`is_cold_storage`](../references/database-tables.md#hub_address) field to 1 for any Hub addresses in the `hub_address` table row that you want to use as cold storage.

3. Save the seed UUIDs of your chosen Hub addresses, then delete it from Hub

4. Use the seed UUID (and the salt if you set one) to recreate the seed for your chosen Hub addresses outside of Hub

5. Use the seed to transfer the IOTA tokens from the Hub addresses to the new address outside of Hub

6. Restart Hub

:::info:
If enough interest exists for storing tokens outside of Hub, we can create a specialized endpoint that makes this task easier. Please reach out to us on [Discord](https://discord.iota.org).
:::

### Send IOTA tokens back to Hub

To process withdrawal requests, you may need to transfer IOTA tokens back into Hub so that it can use them in a sweep.

1. Stop Hub

2. Set the `is_cold_storage` field to 0 for any Hub addresses in the `hub_address` table row that you want to send tokens to

3. Restore the seed UUID for the addresses in the Hub database

4. Restart Hub

## Integration option 2. Manage user balances outside of Hub

By managing user balances outside of Hub, it's easier to store tokens outside of Hub. But, you need to keep track of users' balances in your own system.

### Prerequisites

1. Create a cold wallet, using a new seed
2. Create a new Hub user to use as a hot wallet

---

|**User action**|**Exchange action**|**Hub endpoint**|
|:----------|:--------------|:-----------|
|Signs up for an IOTA account on the exchange|Creates a new user in Hub| `CreateUser`|
|Requests a deposit address in which to deposit IOTA tokens|Creates a new deposit address for the user|`GetDepositAddress`|
|Deposits IOTA tokens into the address|Notifies the user on the exchange frontend after the deposit has been swept to one of the Hub owner's new addresses. Then, updates the user's balance on the exchange backend.|`BalanceSubscription`|
| |Updates the Hub database so that the hot wallet owns all the IOTA tokens|`ProcessTransfers`|
|Requests a withdrawal to an address outside of Hub|Actions the withdrawal from the hot wallet to the user's chosen address|`UserWithdraw`|
|Buys tokens from another user|When users buy and sell cryptocurrencies on the exchange, nothing is recorded in Hub because as far as Hub is aware, the hot wallet owns all the tokens. As a result, the exchange must handle all the accounting outside of Hub.|None|

### Store IOTA tokens outside of Hub

To store IOTA tokens outside of Hub, you need to transfer them from the hot wallet addresses to a cold wallet address.

:::warning:Warning
When you transfer tokens outside of Hub addresses, you're at risk of not being able to process withdrawal requests.
:::

1. Action a withdrawal from the hot wallet to a new address, using the `UserWithdraw` endpoint

  :::warning:Warning
  In this scenario, Hub cannot check whether the new address was already withdrawn from.
  :::

### Send IOTA tokens back to Hub

To process withdrawal requests, you may need to transfer IOTA tokens back into Hub so that it can use them in a sweep.

1. Create a new deposit address for the hot wallet, using the `GetDepositAddress` endpoint

   :::warning:Warning
   Addresses must never be withdrawn from more than once.
   :::

2. Send tokens from the external address to this deposit address

Hub receives the deposit and transfers them to a Hub owner's address as part of a sweep. The tokens in the Hub owner's addresses can be used to fulfil withdrawal requests if the total balance of deposits is not enough.

:::info:
Find out more about [how sweeps work](../concepts/sweeps.md).
:::