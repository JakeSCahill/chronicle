# Run an IRI node on a Linux server

**When you run the IRI software, your device becomes a node that gives you direct access to the Tangle. By running a node, you help the IOTA network to become more distributed by adding to the number of ledgers and validating your neighbors' transactions.**

## Prerequisites

To complete this guide, you need the following:

* 4GB RAM
* 64-bit processor
* An Internet connection
* A Linux server (this guide has been tested on [Ubuntu 18.04](http://releases.ubuntu.com/18.04))
* A [public IP address](root://general/0.1/how-to-guides/expose-your-local-device.md) that's either static or connected to a dynamic DNS service such as [duckdns.org](https://www.duckdns.org)
* [Forward the following ports](root://general/0.1/how-to-guides/expose-your-local-device.md) to the device that's running the node:

    * **TCP neighbor peering port:** 15600
    * **TCP API port:** 14265
    
## Step 1. Download the IRI Java file

The IRI is Java software, so it must be run in a Java runtime environment (JRE).

You have two options for downloading the latest IRI software:
* Download the pre-built Java file from GitHub (quickest option)
* Build the Java file from the source code on GitHub 

### Download the pre-built IRI Java file

The pre-built IRI Java file is available on the [IOTA GitHub repository](https://github.com/iotaledger/iri/releases). Downloading this file is the quickest and simplest way to install the IRI.

1. Install the latest security patches for your system

    ```bash
    sudo apt-get update
    sudo apt-get upgrade -y
    ```

2. Make a directory in which to download the IRI Java file. Change `jake` to your Linux username.

    ```bash
    mkdir /home/jake/node
    ```
    :::info:
    If you see 'mkdir: cannot create directory...' in the output, you probably copied and pasted the command without changing `jake` to your Linux username.
    :::

3. Download and install the Java 8 OpenJDK

    ```bash
    sudo add-apt-repository universe
    sudo apt-get install -y software-properties-common --no-install-recommends
    sudo apt-get install openjdk-8-jdk
    sudo apt-get update
    ```

    :::info:
    To check that Java is installed, enter `java -version`. You should see a version number in the output.
    :::


4. Download the latest IRI Java file into your `node` directory. Change `jake` to your Linux username and replace the `${VERSION}` variable with the [latest version](https://github.com/iotaledger/iri/releases) of the IRI. 

    ```bash
    sudo wget -O /home/jake/node/iri-${VERSION}.jar https://github.com/iotaledger/iri/releases/download/v${VERSION}/iri-${VERSION}.jar
    ```

    :::info:
    Make sure that you include the whole version, for example 1.6.0-RELEASE.
    :::

The download may take some time. You should see something like the following in the output if everything went well:

```
HTTP request sent, awaiting response ... 200 OK
'/home/jake/node/iri-1.6.0-RELEASE.jar' saved [175441686/175441686]
```

:::info:Is this your first node?
You need to download the `spent-addresses-db` directory from [our website](https://dbfiles.iota.org/?prefix=mainnet/spent-addresses/), [the IOTA Partners website](https://iota.partners/#database), or the [IOTA Playbook](https://iri-playbook.readthedocs.io/en/master/faq.html#where-can-i-get-a-fully-synced-database-to-help-kick-start-my-node).

After you've downloaded the directory, extract it into the same directory as your IRI Java file from step 4. For this example, the file is in the `/home/jake/node/` directory.
:::

Now that the IRI Java file and the `spent-addresses-db` directory are saved on your server, [configure the IRI](#configure-the-iri) before running it.

### Build the IRI Java file from the source code

Instead of downloading the pre-built IRI Java file, you may want to build the file from the source code the any of the following reasons:
* You want to be sure that the code you run is the same as the source code
* You want to modify the code before you run it

1. Download and install the Java 8 OpenJDK

    ```bash
    sudo apt-get install -y software-properties-common --no-install-recommends
    sudo apt-get install openjdk-8-jdk
    sudo apt-get update
    ```

2. Install the [Maven](https://maven.apache.org/what-is-maven.html) build tool. Change the `USER_HOME_DIR` variable to your chosen path.

    ```bash
    export MAVEN_VERSION=3.5.4
    export USER_HOME_DIR="/root"
    export SHA=ce50b1c91364cb77efe3776f756a6d92b76d9038b0a0782f7d53acf1e997a14d
    export BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries
    sudo apt-get update && apt-get install -y --no-install-recommends curl
    sudo mkdir -p /usr/share/maven /usr/share/maven/ref
    sudo curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz

    # Check the sha256 checksum, the output should read 'OK' if the checksum is correct

    echo "${SHA} /tmp/apache-maven.tar.gz" | sha256sum -c -
    sudo tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1
    sudo rm -f /tmp/apache-maven.tar.gz
    export MAVEN_HOME=/usr/share/maven
    export MAVEN_CONFIG="${USER_HOME_DIR}/.m2"
    ```

    :::info:
    The SHA256 checksum is also available on the [Apache website](https://archive.apache.org/dist/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz.sha256).
    :::

3. Install Git

    ```bash
    sudo apt-get update && apt-get install -y --no-install-recommends git
    ```

4. Clone and check out the GitHub repository

    ```bash
    git clone https://github.com/iotaledger/iri.git
    cd iri

    # Checkout the latest tag

    export TAG=$(git describe --tags $(git rev-list --tags --max-count=1))
    git checkout ${TAG}
    ```

5. Build the IRI Java file

    ```bash
    /usr/share/maven/bin/mvn clean package
    ```
    :::info:
    The IRI Java file is in a directory called `target`.
    :::

## Step 2. Configure the IRI

The IRI runs in a Java virtual machine. Therefore, before you run the IRI, you need to set up some Java variables.

1. Create the Java variables that'll be used to run the IRI in the Java virtual machine

    ```bash
    export JAVA_OPTIONS="-XX:+UnlockExperimentalVMOptions -XX:+DisableAttachMechanism -XX:InitiatingHeapOccupancyPercent=60 -XX:G1MaxNewSizePercent=75 -XX:MaxGCPauseMillis=10000 -XX:+UseG1GC"
    export JAVA_MIN_MEMORY=2G
    export JAVA_MAX_MEMORY=4G
    ```

    **JAVA_OPTIONS:** Commands that optimise the Java virtual machine

    **JAVA_MIN_MEMORY:** The initial memory allocation for the Java virtual machine
    
    **JAVA_MAX_MEMORY:** the maximum memory allocation for the Java virtual machine
    
2. Create an IRI configuration file in the same directory as your IRI Java file. Change `jake` to your Linux username.

    ```bash
    nano /home/jake/node/config.ini
    ```

    Leave the file empty for now. The default configuration options are fine for this setup. If you want to change the configuration options, edit the config.ini file and add the [configuration options](../references/iri-configuration-options.md) that you want to change.

### Configure a permanode

If you want to run a permanode (keep all transactions in the ledger), set the [`LOCAL_SNAPSHOTS_PRUNING_ENABLED` configuration parameter](../references/iri-configuration-options.md#local-snapshots-enabled) to `false`.

### Configure a Devnet node

If you want to run a Devnet node, you must set the `TESTNET` configuration option to `true`, and add other Devnet nodes to the [`NEIGHBORS`](../references/iri-configuration-options.md#neighbors) configuration parameter.

The following Devnet nodes have auto-peering enabled, so they will automatically add you as neighbors:

* tcp://p101.testnet.iota.cafe:14666

* tcp://p102.testnet.iota.cafe:14666

* tcp://p103.testnet.iota.cafe:14666

* tcp://p104.testnet.iota.cafe:14666

### Configure a Spamnet node

If you want to run a Spamnet node, you must include only the following configuration parameters in your configuration file:

```
[IRI]
ZMQ_ENABLED = TRUE
TESTNET = TRUE
DB_PATH = spamnetdb
DB_LOG_PATH = spamnetdb.log
MWM = 7
SNAPSHOT_FILE = spamnet.txt
COORDINATOR = H9FXUMSYAWNZPVFINVTXOTYKFZXR9OBKA9KSTVWXTWHIZZRISFYZMXIMOQFXDXXQHNAJXAZFP9IHSFXRH
NUMBER_OF_KEYS_IN_A_MILESTONE = 20
SNAPSHOT_TIME = 1535760000
MILESTONE_START_INDEX = 2
DONT_VALIDATE_TESTNET_MILESTONE_SIG = true
NEIGHBORS = tcp://p101.spamnet.iota.cafe:15600 tcp://p102.spamnet.iota.cafe:15600
```

You must also create a snapshot file to define an address that contains the entire supply of tokens on the Spamnet. The location of this file must be set in the `SNAPSHOT_FILE` configuration parameter.

```
WYF9OOFCQJRTLTRMREDWPOBQ9KNDMFVZSROZVXACAWKUMXAIYTFQCPAYZHNGKIWZZGKCSHSSTRDHDAJCW;2779530283277761
```

## Step 3. Run the IRI

When you've downloaded, and configured the IRI, it's time to run it.

1. Make a directory to keep the database and the IXI (IOTA exchange interface folders). Change `jake` to your Linux username.

    ```bash
    mkdir -p /home/jake/node/data
    cd /home/jake/node/data
    ```

    :::info:
    We recommend making regular backups of your database. This way, you can restore your node in case of a corrupted database or another type of node malfunction. To do so, you create a cron job that copies the database to a different volume every day.
    :::

2. Run the IRI. Change `jake` to your Linux username and `$VERSION` to the version of the IRI that you downloaded.

    ```bash
    java ${JAVA_OPTIONS} -Xms${JAVA_MIN_MEMORY} -Xmx${JAVA_MAX_MEMORY} -Djava.net.preferIPv4Stack=true -jar /home/jake/node/iri-${VERSION}.jar
    ```
    The IRI should start to log its activity to the output.

    To make the IRI read your `config.ini` file, add the path to it after the `-c` flag. For example:

    ```bash
    java ${JAVA_OPTIONS} -Xms${JAVA_MIN_MEMORY} -Xmx${JAVA_MAX_MEMORY} -Djava.net.preferIPv4Stack=true -jar /home/jake/node/iri-${VERSION}.jar -c /home/jake/node/config.ini
    ```

    :::success:Congratulations :tada:
    You're now running an IRI node!
    :::

3. Open a new terminal window on your Linux server, and install Curl and JQ. Curl is used to send REST API requests to your IRI node. JQ is a command-line processor that displays JSON data in an easy-to-read format.

    ```bash
    sudo apt install curl jq
    ```

4. Call the [`getNodeInfo`](../references/api-reference.md#getNodeInfo) API endpoint to request general information about the IRI node

    ```bash
    curl -s http://localhost:14265 -X POST -H 'X-IOTA-API-Version: 1' -H 'Content-Type: application/json' -d '{"command": "getNodeInfo"}' | jq
    ```

    You should see something like the following in the output:

    ```json
     {
    "appName": "IRI",
    "appVersion": "1.7.0-RELEASE",
    "jreAvailableProcessors": 8,
    "jreFreeMemory": 2115085674,
    "jreVersion": "1.8.0_191",
    "jreMaxMemory": 20997734400,
    "jreTotalMemory": 4860129502,
    "latestMilestone": "CUOENIPTRCNECMVOXSWKOONGZJICAPH9FIG9F9KYXF9VYXFUKTNDCCLLWRZNUHZIGLJZFWPOVCIZA9999",
    "latestMilestoneIndex": 1050373,
    "latestSolidSubtangleMilestone": "CUOENIPTRCNECMVOXSWKOONGZJICAPH9FIG9F9KYXF9VYXFUKTNDCCLLWRZNUHZIGLJZFWPOVCIZA9999",
    "latestSolidSubtangleMilestoneIndex": 1050373,
    "milestoneStartIndex": -1,
    "lastSnapshottedMilestoneIndex": 1039138,
    "neighbors":0,
    "packetsQueueSize":0,
    "time":1548407444641,
    "tips":0,
    "transactionsToRequest":0,
    "features":["snapshotPruning","dnsRefresher","tipSolidification"],
    "coordinatorAddress": "EQSAUZXULTTYZCLNJNTXQTQHOMOFZERHTCGTXOLTVAHKSA9OGAZDEKECURBRIXIJWNPFCQIOVFVVXJVD9",
    "duration": 0
    }
    ```
    
    You'll notice in the output that the value of the `neighbors` field is 0. The IRI node is not yet connected to an IOTA network. To do so, you need to connect to [neighbor IRI nodes](../concepts/neighbor-iri-node.md).

5. [Find neighbors](../how-to-guides/find-neighbor-iri-nodes.md) and add their URL or IP addresses to your config.ini file

    :::info:
    We recommend [setting up a reverse proxy](../how-to-guides/set-up-a-reverse-proxy.md) for your IRI node so that you can have more control over the requests that are made to it.
    :::

Now that your node is up and running, it'll start to [synchronize its ledger with the network](../concepts/the-ledger.md#ledger-synchronization). Give your node some time to synchronize, or read our troubleshooting guide if your IRI node isn't synchronizing.

## Step 4. Check that the node is synchronized

A node is considered synchronized when the `latestMilestoneIndex` field is equal to the `latestSolidSubtangleMilestoneIndex` field.

The `latestMilestoneIndex` field is the index of the latest milestone that the node has received from its neighbors.

The `latestSolidSubtangleMilestoneIndex` field is the index of the latest milestone for which the node's ledger has all the transactions that the milestone directly and indirectly references.

The `latestMilestoneIndex` and `latestSolidSubtangleMilestoneIndex` fields are accurate only when the node is connected to synchronized neighbors.

1. To check the current `latestMilestoneIndex` field, go to our [Discord](https://discord.iota.org) and enter **!milestone** in one of the channels

    ![Entering !milestone on Discord](../images/discord-milestone-check.PNG)

2. To check these fields for your IRI node, call the `getNodeInfo` API endpoint

    ```bash
    sudo apt install curl jq
    curl -s http://localhost:14265 -X POST -H 'X-IOTA-API-Version: 1' -H 'Content-Type: application/json' -d '{"command": "getNodeInfo"}' | jq
    ```

:::info:
It may take some time for the IRI to synchronize. For help with any issues, read our [troubleshooting guide](../references/troubleshooting.md).
:::

## Next steps

[Subscribe to events in an IRI node](../how-to-guides/subscribe-to-events-in-an-iri-node.md)
