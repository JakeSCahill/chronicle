# API reference

**Hub has a gRPC API that simplifies connections to the Tangle and the Hub database. Use this API reference to find methods and learn what they do.**

This documentation is written assuming you already have an understanding of gRPC.

:::info:
If you’re not familiar with gRPC and protobuf, we recommend following the [gRPC quickstart guide](https://grpc.io/docs/quickstart/).
:::

<a name="hub.proto"></a>

## hub.proto

<a name="hub.rpc.Hub"></a>

### Hub

| **Method name**          | **Request type**                                                 | **Response type**                                                | **Description**                                                  |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| CreateUser           | [CreateUserRequest](#hub.rpc.CreateUserRequest)              | [CreateUserReply](#hub.rpc.CreateUserRequest)                | Create a new user on the Hub                                |
| GetBalance           | [GetBalanceRequest](#hub.rpc.GetBalanceRequest)              | [GetBalanceReply](#hub.rpc.GetBalanceRequest)                | Get the available balance for a user                     |
| GetDepositAddress    | [GetDepositAddressRequest](#hub.rpc.GetDepositAddressRequest) | [GetDepositAddressReply](#hub.rpc.GetDepositAddressRequest)  | Create a new deposit address for a user                     |
| UserWithdraw         | [UserWithdrawRequest](#hub.rpc.UserWithdrawRequest)          | [UserWithdrawReply](#hub.rpc.UserWithdrawRequest)            | Submit a withdrawal request for a user.                     |
| UserWithdrawCancel   | [UserWithdrawCancelRequest](#hub.rpc.UserWithdrawCancelRequest) | [UserWithdrawCancelReply](#hub.rpc.UserWithdrawCancelRequest) | Submit a request to cancel a withdrawal. A cancelation is possible only if it isn't already included in a sweep. |
| GetUserHistory       | [GetUserHistoryRequest](#hub.rpc.GetUserHistoryRequest)      | [GetUserHistoryReply](#hub.rpc.GetUserHistoryRequest)        | Get a user's balance history |
| ProcessTransferBatch | [ProcessTransferBatchRequest](#hub.rpc.ProcessTransferBatchRequest) | [ProcessTransferBatchReply](#hub.rpc.ProcessTransferBatchRequest) | Process a batch of buys/sells from the exchange. Note that the total amount of this batch must sum to 0. |
| BalanceSubscription  | [BalanceSubscriptionRequest](#hub.rpc.BalanceSubscriptionRequest) | [BalanceEvent](#hub.rpc.BalanceSubscriptionRequest)          | Monitor a stream of all balance changes since the provided timestamp |
| GetStats             | [StatsRequest](#hub.rpc.StatsRequest)                        | [StatsReply](#hub.rpc.StatsRequest)                          | Get the total balance of all users that Hub currently manages                            |
| SweepSubscription    | [SweepSubscriptionRequest](#hub.rpc.SweepSubscriptionRequest) | [SweepEvent](#SweepEvent)              | Monitor a stream of all sweeps since the provided timestamp |
| GetAddressInfo       | [GetAddressInfoRequest](#hub.rpc.GetAddressInfoRequest)      | [GetAddressInfoReply](#hub.rpc.GetAddressInfoRequest)        | Get the ID of the user that owns a deposit address |
| SweepInfo            | [SweepInfoRequest](#hub.rpc.SweepInfoRequest)                | [SweepEvent](#SweepEvent)                      | Get information about the sweep for the given withdrawal or bundle hash |
| SignBundle           | [SignBundleRequest](#hub.rpc.SignBundleRequest)              | [SignBundleReply](#hub.rpc.SignBundleRequest)                | Get a signature for a bundle                       |
| SweepDetail          | [SweepDetailRequest](#hub.rpc.SweepDetailRequest)            | [SweepDetailReply](#hub.rpc.SweepDetailRequest)              | Get detailed information about a sweep |
| WasWithdrawalCancelled          | [WasWithdrawalCancelledRequest](#hub.rpc.WasWithdrawalCancelledRequest)            | [WasWithdrawalCancelledReply](#hub.rpc.WasWithdrawalCancelledReply)              | Find out if a withdrawal was canceled |

<a name="messages.proto"></a>

## messages.proto

<a name="hub.rpc.BalanceSubscriptionRequest"></a>

### BalanceSubscriptionRequest

|**Field**| **Type**| **Rules**|**Description**|
| --------- | ----------------- | ----- | ------------------------------------------------------------ |
| newerThan | [uint64](#uint64) |  singular     | Returns a `BalanceEvent` object for any balance changes, starting from this time in milliseconds since the Unix epoch|

<a name="hub.rpc.BalanceEvent"></a>

### BalanceEvent

| **Field**            | **Type** | **Rules** | **Description** |
| ---------------- | ----------------------------------------------------------- | ----- | ----------- |
| userAccountEvent | [UserAccountBalanceEvent](#hub.rpc.UserAccountBalanceEvent) |     singular  |   An event object that contains details about changes to the balance of a user's account          |
| userAddressEvent | [UserAddressBalanceEvent](#hub.rpc.UserAddressBalanceEvent) |     singular  |    An event object that contains details about changes to the balance of a user's deposit address         |
| hubAddressEvent  | [HubAddressBalanceEvent](#hub.rpc.HubAddressBalanceEvent)   |     singular  |    An event object that contains details about changes to the balance of one of the Hub owner's addresses         |

<a name="hub.rpc.CreateUserRequest"></a>

### CreateUserRequest

|**Field**| **Type**| **Rules** |**Description**|
| ------ | ----------------- | ----- | ----------- |
| userId | [string](#string) |   singular    | A unique ID for the new user|

<a name="hub.rpc.CreateUserReply"></a>

### CreateUserReply

Currently not used.

<a name="hub.rpc.GetAddressInfoRequest"></a>

### GetAddressInfoRequest

|**Field**|**Type**|**Rules**|**Description**|
| ------- | ----------------- | ----- | ---------------------- |
| address | [string](#string) |   singular    | The deposit address for which you want to find the owner |

<a name="hub.rpc.GetAddressInfoReply"></a>

### GetAddressInfoReply

|**Field**| **Type**|**Rules** |**Description**| 
| ------ | ----------------- | ----- | --------------------------------------- |
| userId | [string](#string) |   singular    | The ID of the user who owns the deposit address |

<a name="hub.rpc.GetBalanceRequest"></a>

### GetBalanceRequest

|**Field**| **Type**| **Rules**|**Description**| 
| ------ | ----------------- | ----- | ----------- |
| userId | [string](#string) |   singular    |  The ID of the user whose balance you want to see           |

<a name="hub.rpc.GetBalanceReply"></a>

### GetBalanceReply

|**Field**|**Type**|**Rules**|**Description**|
| --------- | --------------- | ----- | ------------------------------------------------------------ |
| available | [int64](#int64) | singular      | The user's total balance that is currently available for withdrawals and trades |

<a name="hub.rpc.GetDepositAddressRequest"></a>

### GetDepositAddressRequest

|**Field**|**Type**|**Rules**|**Description**|
| --------------- | ----------------- | ----- | ----------- |
| userId          | [string](#string) |   singular    |  The ID of the user for whom you want to create a new deposit address          |
| includeChecksum | [bool](#bool)     |   singular    |  Whether you want the address to include a 9 tryte checksum at the end           |

<a name="hub.rpc.GetDepositAddressReply"></a>

### GetDepositAddressReply

|**Field**|**Type**|**Rules** |**Description**|
| ------- | ----------------- | ----- | ----------------------------- |
| address | [string](#string) |  singular     | A new deposit address |

<a name="hub.rpc.GetUserHistoryRequest"></a>

### GetUserHistoryRequest

|**Field**|**Type**|**Rules**|**Description**| 
| --------- | ----------------- | ----- | ------------------------------------------------------------ |
| userId    | [string](#string) |   singular    | UserId                                                       |
| newerThan | [uint64](#uint64) |  singular     | Get a list of `UserAccountBalanceEvent` objects that occured after this unix epoch in milliseconds  |

<a name="hub.rpc.GetUserHistoryReply"></a>

### GetUserHistoryReply

|**Field**|**Type**| **Rules**    |**Description**|
| ------ | ----------------------------------------------------------- | -------- | ---------------------------------------------- |
| events | [UserAccountBalanceEvent](#hub.rpc.UserAccountBalanceEvent) objects | repeated | List of all balance event objects for given user |

<a name="hub.rpc.HubAddressBalanceEvent"></a>

### HubAddressBalanceEvent

|**Field**|**Type**|**Rules**|**Description**|
| --------------- | ----------------------------------------------------------- | ----- | ----------- |
| hubAddress      | [string](#string)                                           |      singular |   Hub owner's address          |
| amount          | [int64](#int64)                                             |     singular  |  Amount that changed to the balance       |
| reason          | [HubAddressBalanceReason](#hub.rpc.HubAddressBalanceReason) |     singular  |     Reason why the balance changed        |
| sweepBundleHash | [string](#string)                                           |      singular |   Bundle hash of the sweep that resulted in the updated balance          |
| timestamp       | [uint64](#uint64)                                           |      singular |   The Unix epoch in milliseconds that the event occured          |

<a name="hub.rpc.ProcessTransferBatchReply"></a>

### HubAddressBalanceReason

|**Name**|        **Number**| **Description**                               |
| ----------- | ------ | ----------------------------------------- |
| HUB_UNKNOWN | 0      |                                           |
| INBOUND     | 1      | Sweep inbound (used as remainder address) |
| OUTBOUND    | 2      | Sweep outbound (used as input)            |

### ProcessTransferBatchReply

<a name="hub.rpc.ProcessTransferBatchRequest"></a>

### ProcessTransferBatchRequest

| **Field**     | **Type**| **Rules**    | **Description** |
| --------- | ------------------------------------------------------------ | -------- | ----------- |
| transfers | [ProcessTransferBatchRequest.Transfer](#hub.rpc.ProcessTransferBatchRequest.Transfer) objects | repeated | Transfer objects that specify which users' accounts to use during the transfer         |

<a name="hub.rpc.ProcessTransferBatchRequest.Transfer"></a>

### ProcessTransferBatchRequest.Transfer

|**Field**|**Type**|**Rules**|**Description**|
| ------ | ----------------- | ----- | ----------- |
| userId | [string](#string) |  singular     |   ID of the user whose balance you want to use during the transfer          |
| amount | [int64](#int64)   |  singular     |   Amount of tokens for the transfer          |

<a name="hub.rpc.SignBundleRequest"></a>

### SignBundleRequest

|**Field**|**Type**|**Rules**|**Description**|
| ---------------- | ----------------- | ----- | ------------------------------------------------------------ |
| address          | [string](#string) | singular      | The Hub user's address that you want to withdraw from (without checksum) |
| bundleHash       | [string](#string) |  singular     | The bundle hash that needs signing                      |
| authentication   | [string](#string) |  singular     | Authentication token (if used)                                       |
| validateChecksum | [bool](#bool)     |   singular    | Whether to validate the address                            |

<a name="hub.rpc.SignBundleReply"></a>

### SignBundleReply

|**Field**|**Type**|**Rules** |**Description**|
| --------- | ----------------- | ----- | ----------------------- |
| signature | [string](#string) |   singular    | The signature for the bundle |

<a name="hub.rpc.StatsRequest"></a>

### StatsRequest

<a name="hub.rpc.SweepDetailRequest"></a>

<a name="hub.rpc.StatsReply"></a>

### StatsReply

|**Field**|**Type**|**Rules** |**Description**|
| ------------ | ----------------- | ----- | ------------------------------------------- |
| totalBalance | [uint64](#uint64) |   singular    | Total balance of all user accounts that are Hub currently manages |

### SweepDetailRequest

|**Field**|**Type**|**Rules** |**Description**|
| ---------- | ----------------- | ----- | ----------------------- |
| bundleHash | [string](#string) | singular      | The bundle hash of the sweep for which you want details |

<a name="hub.rpc.SweepDetailReply"></a>

### SweepDetailReply

|**Field**|     **Type**   |**Rules**    |**Description**|      
| --------- | ----------------- | -------- | ------------------------------------------------------------ |
| confirmed | [bool](#bool)     |singular          | The sweep's confirmation status                              |
| trytes    | [string](#string) | repeated | The sweep's transactions trytes                              |
| tailHash  | [string](#string) | repeated | The sweep's tail transaction hashes (each reattached sweep results in a new tail transaction hash) |

<a name="hub.rpc.SweepEvent"></a>

### SweepEvent

|**Field**|**Type**|**Rules**|**Description**|
| -------------- | ----------------- | -------- | ----------- |
| bundleHash     | [string](#string) |   singular       | The bundle hash of the sweep            |
| timestamp      | [uint64](#uint64) |   singular       |   The Unix epoch in milliseconds of when the sweep was created        |
| withdrawalUUID | [string](#string) | repeated |    The UUIDs of any user withdrawal requests that were included in the sweep       |

<a name="hub.rpc.SweepInfoRequest"></a>

### SweepInfoRequest

|**Field**|**Type**|**Rules** |**Description**|
| -------------- | ----------------- | ----- | -------------------------- |
| withdrawalUUID | [string](#string) |  singular     | The withdrawal UUID to check for inclusion in a sweep |
| bundleHash     | [string](#string) |   singular    | The bundle hash of the sweep to check |

<a name="hub.rpc.SweepSubscriptionRequest"></a>

### SweepSubscriptionRequest

|**Field**|**Type**|**Rules**|**Description**|
| --------- | ----------------- | ----- | ------------------------------------------------------------ |
| newerThan | [uint64](#uint64) | singular      | Monitor Hub for sweeps that are newer that this Unix epoch in milliseconds |

<a name="hub.rpc.UserAccountBalanceEvent"></a>

### UserAccountBalanceEvent

|**Field**| **Type**|**Rules**|**Description**|
| --------------- | ------------------------------------------------------------ | ----- | ------------------------------------------------------------ |
| userId          | [string](#string)                                            |       singular|                        ID of the user whose account's balance changed                                      |
| timestamp       | [uint64](#uint64)                                            |     singular  | Time since epoch in MS when the balance change occured       |
|type|     [UserAccountBalanceEventType](#hub.rpc.UserAccountBalanceEventType)       |  singular|   The type of event that caused a change to the account balance    |                                                              |
| amount          | [int64](#int64)                                         | singular    |   Amount that changed to the balance     |        
| sweepBundleHash or withdrawalUUID  | [string](#string)                                            |      singular | Contains either a bundle hash for a `DEPOSIT` event or a withdrawal UUID for a `WITHDRAWAL` or `WITHDRAWAL_CANCELED` event|

<a name="hub.rpc.UserAccountBalanceEventType"></a>

### UserAccountBalanceEventType

|**Name**|**Number**|**Description**  |
| :------------------- | :------ | :------------------------------------------------------------ |
| UAB_UNKNOWN         | 0      | Unused                                                       |
| DEPOSIT             | 1      | Deposit into user account (positive amount)                  |
| BUY                 | 2      | User received tokens as part of a transfer batch (positive amount) |
| WITHDRAWAL          | 3      | User withdrawal request (negative amount)                    |
| WITHDRAWAL_CANCELED | 4      | Canceled user withdrawal request (positive amount)          |
| SELL                | 5      | User lost tokens as part of a transfer batch (negative amount) |

<a name="hub.rpc.UserAddressBalanceEvent"></a>

### UserAddressBalanceEvent

|**Field**|**Type**|**Rules** |**Description**|
| ----------- | ------------------------------------------------------------ | ----- | ------------------------------------------------------------ |
| userId      | [string](#string)                                            |singular|                        ID of the user whose account's balance changed  |
| userAddress | [string](#string)                                            |      singular |    Address whose balance was changed                                                          |
| amount      | [int64](#int64)                                              |       singular|     Amount that changed to the balance                                                          |
| reason      | [UserAddressBalanceReason](#hub.rpc.UserAddressBalanceReason) |      singular |    The reason that the balance of the address changed                                                          |
| tail transaction hash or bundle hash       | [string](#string)                                            |   singular    | Contains either a tail transaction hash for a `DEPOSIT` reason or a bundle hash for a `SWEEP` reason |
| timestamp   | [uint64](#uint64)                                            |      singular |       Time since epoch in MS when the balance change occured                                                        |

<a name="hub.rpc.UserAddressBalanceReason"></a>

### UserAddressBalanceReason

|**Name**|         **Number**| **Description**             |
| ------------ | ------ | ------------------------ |
| UADD_UNKNOWN | 0      |    Unknown                      |
| UA_DEPOSIT   | 1      | New user deposit |
| UA_SWEEP     | 2      | New sweep              |

<a name="hub.rpc.UserWithdrawCancelRequest"></a>

### UserWithdrawCancelRequest

|**Field**|**Type**|**Rules**|**Description**|
| ----- | ----------------- | ----- | -------------------------------------- |
| withdrawalUUID  | [string](#string) |   singular    | Withdrawal UUID that you want to cancel |

<a name="hub.rpc.UserWithdrawCancelReply"></a>

### UserWithdrawCancelReply

|**Field**|**Type**|**Rules** |**Description**|
| ------- | ------------- | ----- | ------------------------------------ |
| success | [bool](#bool) |   singular    | Whether the withdrawal was canceled |

<a name="hub.rpc.UserWithdrawRequest"></a>

### UserWithdrawRequest

|**Field**|**Type**| **Rules**| **Description**|
| ---------------- | ----------------- | ----- | ------------------------------------------------------------ |
| userId      | [string](#string)                                            |singular|       ID of the user who requests the withdrawal
| payoutAddress    | [string](#string) |  singular     | The address that the withdrawal should be deposited into (without checksum) |
| amount           | [uint64](#uint64) |  singular     | The requested withdrawal amount                                  |
| tag              | [string](#string) |  singular     | The tag to add to the withdrawal transaction                                           |
| validateChecksum | [bool](#bool)     |  singular     | Whether to validate the address                              |

<a name="hub.rpc.UserWithdrawReply"></a>

### UserWithdrawReply

|**Field**|**Type**|**Rules** |**Description**|
| ----- | ----------------- | ----- | -------------------------- |
| withdrawalUUID  | [string](#string) |  singular     | This withdrawal's UUID |

<a name="hub.rpc.WasWithdrawalCancelledRequest"></a>

### WasWithdrawalCancelledRequest

|**Field**|**Type**|**Rules**|**Description**|
| ---------------- | ----------------- | ----- | ------------------------------------------------------------ |
| withdrawalUUID  | [string](#string) |  singular     | Withdrawal UUID to check for cancelation |

<a name="hub.rpc.WasWithdrawalCancelledReply"></a>

### WasWithdrawalCancelledReply

|**Field**|**Type**|**Rules**|**Description**|
| ---------------- | ----------------- | ----- | ------------------------------------------------------------ |
| wasCancelled  | [bool](#bool) |  singular     | Whether the withdrawal was canceled |

<a name="hub.rpc.Error"></a>

<a name="hub.rpc.HubAddressBalanceReason"></a>

### Error

Errors are serialized and stored in the Status' detail field.

|**Field**|**Type**|**Rules** |**Description**|
| ----- | ------------------------------- | ----- | ----------- |
| code  | [ErrorCode](#hub.rpc.ErrorCode) |   singular    | The error code that explains why a method was unsuccessful            |

<a name="hub.rpc.ErrorCode"></a>

### ErrorCode

These are all the error codes that can be returned by the Hub.

|**Name**|**Number**|**Description**|
| :------------------------------- | :------ | :------------------------------------------------------------ |
| EC_UNKNOWN                      | 0      | Unused|                                              
| USER_EXISTS                     | 1      | UserId already exists|                     
| USER_DOES_NOT_EXIST             | 2      | UserId does not exist                                   |
| INSUFFICIENT_BALANCE            | 3      | The user has insufficient balance for this operation        |
| BATCH_INVALID                   | 4      | The batch is invalid (does not sum to 0 or user ids not unique) |
| BATCH_INCONSISTENT              | 5      | The batch is inconsistent (attempt to remove funds from a user's account without sufficient balance) |
| BATCH_AMOUNT_ZERO               | 6      | The amount associated with the transfer is invalid (should be greater or less than 0) |
| UNKNOWN_ADDRESS                 | 7      | The address is not known to the hub                          |
| WITHDRAWAL_CAN_NOT_BE_CANCELLED | 8      | Either the withdrawal has been swept or cancelled already    |
| INELIGIBLE_ADDRESS              | 9      | Address was not eligible for the requested operation.        |
| INVALID_AUTHENTICATION          | 10     | Provided authentication token was invalid.                   |
| CHECKSUM_INVALID                | 11     | Provided address contained invalid checksum                  |
| SIGNING_FAILED                  | 12     | Call to rpc signing_server failed (GetSignatureForUUID)      |
| GET_ADDRESS_FAILED              | 13     | Call to rpc signing_server failed (GetAddressForUUID)        |
| GET_SECURITY_LEVEL_FAILED       | 14     | Call to rpc signing_server failed (GetSecurityLevel)         |

## Scalar Value Types

| **.proto type**                    | **Notes**                                                        | **C++ type** | **Java type**  | **Python type** |
| :------------------------------ | :------------------------------------------------------------ | :-------- | :---------- | :----------- |
| <a name="double" /> double     |                                                              | double   | double     | float       |
| <a name="float" /> float       |                                                              | float    | float      | float       |
| <a name="int32" /> int32       | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint32 instead. | int32    | int        | int         |
| <a name="int64" /> int64       | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint64 instead. | int64    | long       | int/long    |
| <a name="uint32" /> uint32     | Uses variable-length encoding.                               | uint32   | int        | int/long    |
| <a name="uint64" /> uint64     | Uses variable-length encoding.                               | uint64   | long       | int/long    |
| <a name="sint32" /> sint32     | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int32s. | int32    | int        | int         |
| <a name="sint64" /> sint64     | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int64s. | int64    | long       | int/long    |
| <a name="fixed32" /> fixed32   | Always four bytes. More efficient than uint32 if values are often greater than 2<sup>28</sup>. | uint32   | int        | int         |
| <a name="fixed64" /> fixed64   | Always eight bytes. More efficient than uint64 if values are often greater than 2<sup>56</sup>. | uint64   | long       | int/long    |
| <a name="sfixed32" /> sfixed32 | Always four bytes.                                           | int32    | int        | int         |
| <a name="sfixed64" /> sfixed64 | Always eight bytes.                                          | int64    | long       | int/long    |
| <a name="bool" /> bool         |                                                              | bool     | boolean    | boolean     |
| <a name="string" /> string     | A string must always contain UTF-8 encoded or 7-bit ASCII text. | string   | String     | str/Unicode |
| <a name="bytes" /> bytes       | May contain any arbitrary sequence of bytes.                 | string   | ByteString | str         |

