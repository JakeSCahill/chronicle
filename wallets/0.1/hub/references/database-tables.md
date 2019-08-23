# Database tables

**Hub manages users' IOTA tokens by recording information such as deposit addresses and withdrawal requests in database tables. You can find this information by using the gRPC API or by querying the database server.**


| **Table**      | **Description**|
|:-----------|:------------|
| `hub_address`          | About the Hub owner's addresses. These addresses are where remainder tokens are sent during sweeps.|
| `hub_address_balance`  |About updates to the balance of the Hub owner's addresses. Hub updates this table whenever it completes an action that involves one of the addresses.|
| `signed_uuids`         |A record of the seed UUIDs for addresses that have been withdrawn from (also known as used or spent) |
| `sweep`                | About confirmed and pending sweeps|
| `sweep_tails`          |About the tail transactions of sweeps. Hub uses this table to check if a sweep is confirmed. |
| `user_account`         |About users' Hub accounts |
| `user_account_balance` | About the balances of users' Hub accounts|
| `user_address`         |About the users' deposit addresses |
| `user_address_balance` |About the balance of users' deposit addresses. Hub updates this table whenever it completes an action that involves one of the addresses. |
| `withdrawal`           | About the balance of users' withdrawal requests|


## Hub_address

This table contains information about the Hub owner's addresses to which the tokens in users' deposit addresses are transferred in sweeps.

| **Field**           | **Description**          |**Default**|
|:---------------|:-----------------------|:-------------------|
| `id`              | Unique ID of the Hub owner's addresses |NULL|
| `address`         | 81-tryte address (no checksum)|          NULL      |
| `seed_uuid`       | Universally unique identifier. This string is used in the [Argon2](https://www.argon2.com/) hashing function to create the seed that derives the address. When Hub is started with a `salt` flag, the value of that flag is also used in the hashing function to create the seed. |        NULL        |
| `is_cold_storage` | Disable sweeps for this address| 0|
| `balance`         | Total amount of IOTA tokens in the address|    0            |
| `created_at`      | Date and time that the address was saved in the following format: `YYYY-MM-DD HH:MM:SS`| The current date and time

## Hub_address_balance

This table contains information about updates to the balance of the Hub owner's addresses.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `id`          | Unique ID of the balance update event |NULL
| `hub_address` | ID of the address in the `hub_address` table|NULL
| `amount`      | Total amount of IOTA tokens in the address |0
| `reason`      |One of [three reasons](../references/api-reference.md#hub.rpc.HubAddressBalanceReason) for the balance update event|NULL
| `sweep`       | ID of the sweep in the `sweep` table|NULL
| `occured_at`  |Date and time that the balance was updated in the following format: `YYYY-MM-DD HH:MM:SS` |The current date and time|

## Signed_uuids

This table contains the seed UUIDs for addresses that have been withdrawn from (also known as used or spent).

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
|`uuid`|Seed UUID for the spent address| NULL|

## Sweep

This table contains information about sweeps.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `id`               |Unique ID of the sweep event | NULL                |
| `bundle_hash`      | 81-tryte bundle hash of the sweep| NULL                |
| `trytes`           |Transaction trytes of the sweep | NULL                |
| `into_hub_address` |ID of the address to which the remainder of the sweep was sent to. This ID is in the `hub_address` table.  | NULL                |
| `confirmed`        |Whether the sweep is confirmed. 0=pending. | 0                   |
| `created_at`       | Date and time that the sweep was created in the following format: `YYYY-MM-DD HH:MM:SS` | The current date and time |

## Sweep_tails

This table contains information that Hub uses to check if a sweep is confirmed.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `hash`       |81-tryte tail transaction hash of the sweep | NULL |  
| `sweep`      | ID of the sweep whose bundle includes the tail transaction. This ID is in the `sweep` table.  | NULL|
| `confirmed`  | Whether the sweep is confirmed. 0=pending.  | 0 |
| `created_at` | Date and time that the table was created in the following format: `YYYY-MM-DD HH:MM:SS` | The current date and time |

## User_account

This table contains information about the users' Hub accounts.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `id`         |  Unique ID of the user's account. Each user has one account.  | NULL    |
| `balance`    | Total amount of IOTA tokens that the user owns in Hub  |  0       | 
| `identifier` | Hub-owner-defined ID for the user |NULL    |  


## User_account_balance

This table contains information about the balances of users' Hub accounts.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `user_id`    | ID of the user's account in the `user_account` table    | NULL|
| `amount`     | Total amount of IOTA tokens that the user owns in Hub  | NULL |     
| `reason`     | One of [three reasons](../references/api-reference.md#hub.rpc.HubAddressBalanceReason) for the balance update event    | NULL                |
| `sweep`      | ID of the sweep that caused the balance update. This ID is in the `sweep` table.     | NULL                |
| `withdrawal` | ID of the withdrawal in the `withdrawal` table    | NULL                |
| `occured_at` | Date and time that the user's balance was updated in the following format: `YYYY-MM-DD HH:MM:SS` | The current date and time |

## User_address

This table contains information about the users' deposit addresses.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `id`         | Unique ID of the user's address. Each address is derived from a unique seed.   | NULL               |
| `address`    | 81-tryte address (no checksum)   | NULL                |
| `user_id`    | ID of the user's account in the `user_account` table    | NULL |
| `seed_uuid`  |  Universally unique identifier. This string is used in the [Argon2](https://www.argon2.com/) hashing function to create the seed that derives the address. When Hub is started with a `salt` flag, the value of that flag is also used in the hashing function to create the seed. | NULL                |
| `created_at` | Date and time that the address was saved in the following format: `YYYY-MM-DD HH:MM:SS` | The current date and time |
| `balance`    | Total amount of IOTA tokens on the address | 0                   |


## User_address_balance

This table contains information about the balance of users' deposit addresses.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `id`           |  Unique ID of the user's balance update event|NULL|
| `user_address` | ID of the user's address in the `user_address` table    | NULL                |
| `amount`       | Total amount of IOTA tokens on the address | NULL                |
| `reason`       | One of [three reasons](../references/api-reference.md#hub.rpc.HubAddressBalanceReason) for the balance update event    | NULL                |
| `tail_hash`    | 81-tryte tail transaction hash of the sweep   | NULL                |
| `sweep`        | ID of the sweep that caused the balance update. This ID is in the `sweep` table.    | NULL                |
|`message`| Contents (in trytes) of the `signatureMessageFragment` field of the output transaction in a deposit bundle
| `occured_at`   | Date and time that the balance was updated in the following format: `YYYY-MM-DD HH:MM:SS` |The current date and time|

## Withdrawal

This table contains information about the users' withdrawal requests.

| **Field**       | **Description**          | **Default**|
|:----------------|:--------------------------|:--------|
| `id`             |Unique ID of the user's withdrawal event  |NULL                |
| `uuid`           | Universally unique identifier. This string allows users to identify and cancel a withdrawal request. |NULL                |
| `user_id`        | ID of the user's account in the `user_account` table |NULL                |
| `amount`         | Total amount of the withdrawal |NULL                |
| `payout_address` | Output address to which the tokens were sent   |  NULL    |
| `tag`            | Value of the output transaction's [`tag` field](root://dev-essentials/0.1/references/structure-of-a-transaction.md)   |  NULL    |
| `sweep`          | ID of the sweep that actioned the withdrawal. This ID is in the `sweep` table.       | NULL     |
| `requested_at`   | Date and time that the user requested the withdrawal in the following format: `YYYY-MM-DD HH:MM:SS` | The current date and time |
| `cancelled_at`   | Date and time that the withdrawal was canceled in the following format: `YYYY-MM-DD HH:MM:SS` | NULL |
