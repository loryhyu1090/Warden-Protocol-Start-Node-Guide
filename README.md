# Warden-Protocol-Start-Node-Guide
Join Buenavista Guide

![warden](https://github.com/user-attachments/assets/6f5153ac-42d2-4a61-b5d5-8db2f86cf7d8)

## **Join Buenavista Testnet** 🌐

This tutorial explains how to run the `wardend` binary and join the Buenavista testnet. Follow the steps below to get started.

### **Overview**

|Key|Value|
|:--|:----|
|**Chain ID**|`buenavista-1`|
|**Current `wardend` version**|v0.3.1|

#### **Version History**

| Release | Upgrade Block Height | Upgrade Date |
|---------|----------------------|--------------|
| v0.3.0  | genesis              | -            |
| v0.4.0  | -                    | TBD          |

### **Prerequisites** 🛠️

|Key|Value|
|:--|:----|
|**CPU**|8 cores|
|**RAM**|32GB|
|**Storage**|300GB of disk space|

Additionally, you’ll need to install Go.

### **1. Installation** 🚀

To join the Buenavista testnet, you’ll need to install `wardend` (the Warden binary). You can do this in two ways:

#### **Option 1: Use the Prebuilt Binary**

1. Download the binary for your platform from the https://github.com/warden-protocol/wardenprotocol/releases and unzip it. The archive contains the `wardend` binary.

2. Initialize the chain home folder:
    ```bash
    ./wardend init <custom_moniker>
    ```

#### **Option 2: Use the Source Code**

1. Clone the source code and build the `wardend` binary:
    ```bash
    git clone --depth 1 --branch v0.3.0 https://github.com/warden-protocol/wardenprotocol/
    just build
    ```

2. Initialize the chain home folder:
    ```bash
    build/wardend init <custom_moniker>
    ```

### **2. Configure** ⚙️

To configure `wardend`, follow these steps:

1. **Prepare the Genesis File:**
    ```bash
    cd $HOME/.warden/config
    rm genesis.json
    wget https://raw.githubusercontent.com/warden-protocol/networks/main/testnets/buenavista/genesis.json
    ```

2. **Set the Mandatory Configuration Options:**

    - **Set Minimum Gas Price:**
      ```bash
      sed -i 's/minimum-gas-prices = ""/minimum-gas-prices = "0.0025uward"/' app.toml
      ```

    - **Add Persistent Peers:**
      ```bash
      sed -i 's/persistent_peers = ""/persistent_peers = "92ba004ac4bcd5afbd46bc494ec906579d1f5c1d@52.30.124.80:26656,ed5781ea586d802b580fdc3515d75026262f4b9d@54.171.21.98:26656"/' config.toml
      ```

### **3. Set Up State Sync** 🔄

**Note:** This step is recommended but optional.

To speed up the initial sync, use the state sync feature. This allows you to download the state at a specific height from a trusted node and then only download the blocks from the network.

1. **Set the State Sync Variables:**
    ```bash
    export SNAP_RPC_SERVERS="https://rpc.buenavista.wardenprotocol.org:443,https://rpc.buenavista.wardenprotocol.org:443"
    export LATEST_HEIGHT=$(curl -s "https://rpc.buenavista.wardenprotocol.org/block" | jq -r .result.block.header.height)
    export BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000))
    export TRUST_HASH=$(curl -s "https://rpc.buenavista.wardenprotocol.org/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
    ```

2. **Verify the Variables:**
    ```bash
    echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
    ```
    The output should look similar to:
    ```
    70694 68694 6AF4938885598EA10C0BD493D267EF363B067101B6F81D1210B27EBE0B32FA2A
    ```

3. **Add State Sync Configuration:**
    ```bash
    sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
    s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC_SERVERS\"| ; \
    s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
    s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.warden/config/config.toml
    ```

### **4. Start the Node** 🟢

Now you can start the node using the following command:
```bash
wardend start
```
It will connect to the provided persistent peers and start downloading blocks. You can check the logs to monitor the progress.
