# Get started with the gRPC API

**Hub exposes some gRPC methods that you can call using any gRPC client. These methods allow you to manage users' tokens by interfacing with the database and an IOTA network. In this guide, you'll learn the basics of the gRPC API to create a new user with some new deposit addresses.**

:::info:
This guide helps you to test the gRPC API with [this CLI client](https://github.com/njpatel/grpcc). For production environments, we recommend generating client code from one of the available [gRPC libraries](https://grpc.io/about/).
:::

## Prerequisites

You must have [installed Hub](../how-to-guides/install-hub.md) and it must be running on the same server as the one you'll use in this guide.

---

1. Install npm

    ```bash
    sudo apt install -y npm
    ```

2. Install the `grpcc` package

    ```bash
    sudo npm install -g grpcc
    ```

3. Change into the `rpchub` directory

    ```bash
    cd rpchub
    ```

3. Start the gRPC client

    ```bash
    grpcc -i -a localhost:50051 -p proto/hub.proto
    ```

    :::info:
    If you see an error message about a binary not being installed, try removing your `node_modules` directory, then reinstall the `grpcc` package with the following command: `sudo npm install -g --unsafe-perm grpcc`.
    :::
    
    You should see something like the following:

    ```bash
    Connecting to hub.rpc.Hub on localhost:50051. Available globals:

    client - the client connection to Hub
        createUser (CreateUserRequest, callback) returns CreateUserReply
        getBalance (GetBalanceRequest, callback) returns GetBalanceReply
        getDepositAddress (GetDepositAddressRequest, callback) returns GetDepositAddressReply
        userWithdraw (UserWithdrawRequest, callback) returns UserWithdrawReply
        userWithdrawCancel (UserWithdrawCancelRequest, callback) returns UserWithdrawCancelReply
        getUserHistory (GetUserHistoryRequest, callback) returns GetUserHistoryReply
        processTransferBatch (ProcessTransferBatchRequest, callback) returns ProcessTransferBatchReply
        balanceSubscription (BalanceSubscriptionRequest, callback) returns BalanceEvent
        getStats (GetStatsRequest, callback) returns GetStatsReply
        sweepSubscription (SweepSubscriptionRequest, callback) returns SweepEvent
        getAddressInfo (GetAddressInfoRequest, callback) returns GetAddressInfoReply
        sweepInfo (SweepInfoRequest, callback) returns SweepEvent
        signBundle (SignBundleRequest, callback) returns SignBundleReply
        sweepDetail (SweepDetailRequest, callback) returns SweepDetailReply

    printReply - function to easily print a unary call reply (alias: pr)
    streamReply - function to easily print stream call replies (alias: sr)
    createMetadata - convert JS objects into grpc metadata instances (alias: cm)
    printMetadata - function to easily print a unary call's metadata (alias: pm)

    Hub@localhost:50051> (node:6023) DeprecationWarning: grpc.load: Use the @grpc/proto-loader module with grpc.loadPackageDefinition instead
    ```

4. Create a new user

    ```bash
    client.createUser({userId: "Jake"}, pr)
    ```

    :::info:
    You can see in the console from step 3 that the `createUser` method takes a `CreateUserRequest` object. You can search for that object in the [API reference](../references/api-reference.md#hub.rpc.CreateUserRequest).

    The `pr` argument is a pre-built callback function that prints the result to the console.
    :::

    Now, you'll have a new user in the database.

    :::info:
     You can see this user in the database by [querying the `user_account` table](../how-to-guides/query-the-database.md).
     :::

5. Create a new deposit address for the user

    ```bash
    client.getDepositAddress({userId: "Jake"}, pr)
    ```

    You should see a new deposit address in the console.

6. Create a new address with the checksum

    ```bash
    client.getDepositAddress({userId: "Jake", includeChecksum: true}, pr)
    ```

    Now, that user will have two addresses that were created from two different `seeduuid` fields. You can see this data in the database by [querying the `user_address` table](../how-to-guides/query-the-database.md).

    :::info:
    In the database, addresses are always saved without the checksum.

    All addresses are created from unique seeds, and all seeds are created from a hash of the values of the `seeduuid` field and the [`salt`](../references/command-line-flags.md) parameter (if provided).
    :::

7. Press **Ctrl**+**C** twice to stop the gRPC client

:::success:Congratulations :tada:
You've successfully used two gRPC methods to create a new user with two different deposit addresses.
Each of these deposit addresses was derived from a unique seed.
:::

## Next steps

1. Deposit IOTA tokens into one of the user deposit addresses
2. Use the [`sweepSubscription`](../references/api-reference.md#hub.rpc.SweepSubscriptionRequest) method to subscribe to new sweep events. This way, Hub will let you know when a sweep takes place. 
3. Use the [`userWithdraw()`](../references/api-reference.md#hub.rpc.UserWithdrawRequest) method to make a withdrawal request from the deposit address




