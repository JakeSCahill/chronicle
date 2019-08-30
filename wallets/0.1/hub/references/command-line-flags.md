# Command line flags

**When you start Hub or the signing server, you can customize how they function by passing the run command one or more of the following flags.**

## Database flags

| **Flag** |   **Description**| **Default values**|
| :------------------------ | :--------------- | :--------|
|`--db` |Name of the database | "hub"|
|`--dbDebug` |Enable debug mode for the database server connection| false|
|`--dbHost`| URL or IP address of the database server|"127.0.0.1"|
|`--dbPassword`| Password for the database server| "password"|
|`--dbPort`|Database server port| 3306|
|`--dbType`|Type of database| "mariadb"|
|`--dbUser` |Database username| "user"|
|`-fetchTransactionMessages`| Whether Hub stores the message of a deposit transaction in the database |false|

## Hub security flags
 
| **Flag** |   **Description**| **Default values**|
| :------------------------ | :--------------- | :--------|
|`--argon2MCost`|Memory cost of Argon2 in bytes. [See this Stack Exchange topic](https://crypto.stackexchange.com/questions/48954/questions-about-the-argon2-options) for a discussion on this flag.| 1 << 17|
|`--argon2TCost` |Time cost of Argon2. [See this Stack Exchange topic](https://crypto.stackexchange.com/questions/48954/questions-about-the-argon2-options) for a discussion on this flag.|4|
|`--argon2Mode` |Argon2 mode to use: 1=argon2i, 2=argon2id|2|
|`--argon2Parallelism`|Number of threads to use in parallel for Argon2. [See this Stack Exchange topic](https://crypto.stackexchange.com/questions/48954/questions-about-the-argon2-options) for a discussion on this flag.|1|
|`--authMode`|Type of connection encryption that Hub uses. This flag accepts "none" or "ssl" as the value. When set to "ssl", you must also use the `--sslKey`, `--sslCert`, and `sslCA` flags. and |"none"|
|<a name="keySec"></a>`--keySecLevel` |[Security level](root://dev-essentials/0.1/references/security-levels.md) to use for IOTA [addresses](root://dev-essentials/0.1/concepts/addresses-and-signatures.md)|2|
|`--listenAddress`| Host to which the Hub API will listen. The default host allows any IP address to access the API.| "0.0.0.0:50051"|
|`--maxConcurrentArgon2Hash`|Maximum number of concurrent Argon2 hash processes| 4|
|`--salt`| Characters that are hashed together with the `seed_uuid` field to create seeds for each deposit address. This value should be at least 20 characters long.   |   ""    |
|`--sslCert` |Path to the SSL certificate |"/dev/null"|
|`--sslCA` |Path to the certificate authority (CA) root |"/dev/null"|
|`--sslKey` |Path to the SSL certificate key |"/dev/null"|

## IOTA protocol flags

| **Flag** |   **Description**| **Default values**|
| :------------------------ | :--------------- | :--------|
|`--apiAddress`| URL or IP address of the IRI node that Hub connects to. To avoid connecting to a malicious node, we recommend [running your own node](root://node-software/0.1/iri/introduction/overview.md) and connecting to it.| "127.0.0.1:14265"|
|`--attachmentInterval`|Interval in milliseconds that Hub waits between [reattaching and promoting transactions](root://dev-essentials/0.1/concepts/reattach-rebroadcast-promote.md). 0=disabled.|240000|
|`--depth`|Value to use for the `depth` parameter of the [`getTransactionsToApprove` (GTTA)](root://node-software/0.1/iri/references/api-reference.md#getTransactionsToApprove) endpoint| 3|
|`--minWeightMagnitude`| [Minimum weight magnitude (MWM)](root://dev-essentials/0.1/concepts/minimum-weight-magnitude.md) to use for proof of work. To use Hub on the Mainnet, you must use a MWM of 14.| 9|
|`--powMode`|[Proof of work](root://dev-essentials/0.1/concepts/minimum-weight-magnitude.md) mode, local or remote|
|`--useHttpsIRI`| Communicate with the IRI node through the HTTPS protocol (make sure that the node that's specified in the `--apiAddress` flag supports https)|`--nouseHttpsIRI`|

## Signing flags

| **Flag** |   **Description**| **Default values**|
| :------------------------ | :--------------- | :--------|
|`--authProvider`| Provider to use to authenticate the [`signBundle`](../references/api-reference.md#hub.rpc.SignBundleRequest) method. This value can be "none" or "hmac"| "none"|
|`--hmacKeyPath` |Path to the HMAC key used to authenticate the [`signBundle`](../references/api-reference.md#hub.rpc.SignBundleRequest) method|"/dev/null"|
|<a name="signBundle"></a>`--SignBundle_enabled`|Whether you can use the [`SignBundle` endpoint](../references/api-reference.md#hub.rpc.SignBundleRequest)|false|
|`--signingMode`|Method to use to sign bundles. This value can be "local" or "remote" (if you run a signing server) |"local"|
|`--signingProviderAddress`|URL or IP address of the signing server. Where you have the "remote" value for the `--signingMode`, you must also use this flag.|"0.0.0.0:50052"|
|`--signingServerChainCert`|If you configure Hub to use SSL encryption (`--authMode="ssl"`) and you have a [signing server](../how-to-guides/install-the-signing-server.md), you must pass the path to the SSL certificate chain to this flag|"/dev/null"|
|`--signingServerKeyCert`|If you configure Hub to use SSL encryption (`--authMode="ssl"`) and you have a [signing server](../how-to-guides/install-the-signing-server.md), you must pass the path to the SSL certificate key to this flag|"/dev/null"|
|`--signingServerSslCert`|If you configure Hub to use SSL encryption (`--authMode="ssl"`) and you have a [signing server](../how-to-guides/install-the-signing-server.md), you must pass the path to the SSL certificate to this flag|"/dev/null"|

## Sweep flags

| **Flag** |   **Description**| **Default values**|
| :------------------------ | :--------------- | :--------|
|<a name="monitorInterval"></a>`--monitorInterval`|Interval in milliseconds that Hub checks deposit addresses and updates the [`user_address` and `user_address_balance` tables](../references/database-tables.md). Those that contain tokens are included in the next sweep. 0=disabled.|60000|
|`--sweepInterval` |Interval in milliseconds that Hub waits between sweeps (set this flag to 0 to disable sweeps)|600000|
|<a name="sweepLimits"></a>`--sweep_max_deposit`|Maximum number of user deposits to include in a sweep|5|
|`--sweep_max_withdraw`|Maximum number of withdraw requests to include in a sweep|7|
