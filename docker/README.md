Here are the instructions to install the required Rayls components for a **Rayls Privacy Node Operator**.

> **Note:** This guide does **not** cover the setup of components for a **Subnet Operator**, which includes the **Governance APIs and Services**, as well as the **Subnet Hub**, typically a **Hyperledger Besu** node.

For more information, please visit: [https://docs.rayls.com](https://docs.rayls.com)

---

# Rayls Docker Compose Deployment

This repository provides a Docker Compose setup for deploying Rayls components locally for development and testing purposes. It includes services such as **MongoDB**, **Privacy Ledger**, **Relayer**, **KMM**, and **Circom API**.

> [!WARNING]
> The MongoDB provided in this repository and referenced in this documentation is intended solely for **proof-of-concept** and **testing** purposes within the Rayls platform.  
> **Do not use this setup for production environments or in other projects.**

## Prerequisites

Before proceeding, ensure you're using a **Linux-based operating system** with an **amd64** processor architecture.

Additionally, make sure the following components are installed:

- **[Docker](https://www.docker.com/)** (version **28.1** or higher) and **[Docker Compose](https://docs.docker.com/compose/)** (version **2.33.1** or higher)  
Older versions have not been validated for this setup.

To verify your Docker installation, run:
```bash
docker --version
docker compose version
```
✅ Note: Use docker compose (with a space) instead of docker-compose, which is deprecated in newer versions.

- **make** (version 4.3 or higher)

To verify make is installed:
```bash
make --version
```

## Step 1: Clone the Repository and configure

First, clone this repository and navigate to the appropriate directory to run make commands and adjust the environment variables in the .env file:

```bash
git clone https://github.com/raylsnetwork/rayls-setup.git
cd rayls-setup/docker
```

A sample `.env` file is provided in this folder with all the required environment variables.  
Please update the values marked with the comment ```# Change this```.

> [!NOTE]
> This sample `.env` file includes contract information for our **shared Commit Chain**. In this setup, **Parfin** is acting as the **Subnet Operator**, and you are configuring a **Privacy Node Operator** that participates in Parfin's subnet.  
> If you intend to connect to a different subnet, please contact the respective **Subnet Operator** to obtain the appropriate values for the remaining `.env` fields.

Below is an explanation of the key variables to be changed:

### PRIVATE_KEY_SYSTEM

Here we need to fill with a Private Key that will deploy the required contracts in the Privacy Ledger. If you do not have the Private Key, we are providing the command ```make create-private-key``` to generate a new one.

This field is in the Line 9.

### Privacy Ledger Chain ID

This is the chain ID of the Privacy Ledger, if any value if configured it will start with number 1, wich refers to the mainnet, that something that we do not want since its an error. We recommend to set a value with at least 4 digits.

Each participant of the subnet must have a different chainID.

This value must be filled at:
* Line 10: NODE_PL_CHAIN_ID
* Line 13: PARTICIPANT_CHAIN_ID
* Line 24: PARTICIPANT_CHAIN_ID

### Participant Name

This is the identification name of the particpant in the subnet. Must be filled at line 14 PARTICIPANT_NAME

### KMS values

The KMS env values, are only to be used if the KMM module for KMS is enabled. If you are not encrypting the keys that are stored in the database, you do not need to change the `xxx` values.

## Step 2: Initialize the MongoDB Replica Set

As mentioned at the beginning of this documentation, we provide a container image that initializes a MongoDB instance with a **Replica Set**, which is required by the application.

To start MongoDB with the replica set, simply run:

```bash
make mongodb up
```

After running this command, you'll see several log messages on the screen.
Wait until you see the message: REPLICASET ONLINE — this indicates that MongoDB has started and the replica set has been successfully initialized.

Once it's up, you can stop the log output by pressing Control + C.

To view the MongoDB logs again at any time, run: `docker compose logs mongodb`

## Step 3: Initialize the Privacy Ledger

After MongoDB is up and the replica set is online, initialize the Privacy Ledger by running:

```bash
make privacy-ledger up
```
This command will automatically start the Privacy Ledger service and deploy the required smart contracts.

> [!WARNING]
> **Note the contract addresses output.**.
> These values must be copied and added to the corresponding fields in the `.env` file.

> [!CAUTION]
> When the Privacy Ledger starts, it creates the MongoDB database and local data files.  
> If you need to change configuration values — such as the `CHAIN_ID` — you may need to stop MongoDB using:
> ```bash
> make mongodb down
> ```  
> Then, manually delete the data directories of both **Privacy Ledger** and **MongoDB** to avoid conflicts when restarting the services.


## Step 4: Deploy the Relayer and other components

Run the following command to generate the secrets for the KMS and the Relayer:

```bash
make create-relayer-secrets
```

These secrets are automatically saved and injected into the Relayer container as environment variables.

> [!TIP]
> If you encounter a permission error, check whether the scripts/generate_keys.sh file is executable.
> If not, you can make it executable by running:
> `chmod +x scripts/generate_keys.sh`

Now deploy the Relayer and its related components:

```bash
make relayer
```

## Step 5: Verify Services

You can check logs using:

```bash
docker compose logs -f
```

Or individual services:

```bash
docker compose logs -f relayer
```

To inspect containers:

```bash
docker container ls
docker exec -it <container_name> sh
```