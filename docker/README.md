Here are the instructions to install the required Rayls components for a **Rayls Privacy Node Operator**.

> **Note:** This guide does **not** cover the setup of components for a **Subnet Operator**, which includes the **Governance APIs and Services**, as well as the **Subnet Hub**, typically a **Hyperledger Besu** node.

For more information, please visit: [https://docs.rayls.com](https://docs.rayls.com)

---

# Rayls Docker Compose Deployment

This repository provides a Docker Compose setup for deploying Rayls components locally for development and testing purposes. It includes services such as **MongoDB**, **Privacy Ledger**, **Relayer**, **KMM**, and **Circom API**.

> ⚠️ **Warning**  
> The MongoDB provided in this repository and referenced in this documentation is intended solely for **proof-of-concept** and **testing** purposes within the Rayls platform.  
> **Do not use this setup for production environments or in other projects.**

---

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

> [!TIP]
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

## Step 2: Initialize MongoDB Replica Set

As described in the begin of this documentation, we are provinding a container image that initializes a MongoDB cluster with a Replica Set, which is required for the application.

To initialize the MongoDB with the replica set, just run:

```bash
make mongodb up
```

After you run this, many logs will be outputed in the screen. Wait until appear **REPLICASET ONLINE**. It indicates that the mongodb was started and the replicaset initialized. After this you can stop the output just typing Control+C.

If you want to see the logs of the mongodb again, just enter **docker compose logs mongodb**

## Step 3: Initialize Privacy Ledger

After MongoDB is up, initialize the Privacy Ledger:

```bash
make privacy-ledger up
```

This command automatically initializes the Privacy Ledger and deploy the required contracts.

> ⚠️ **Note the contract addresses output.**
> These values must be added at the same field in the `.env` file.

## Step 4: Deploy Relayer

Run the following command to generate KMS and relayer secrets:

```bash
make create-relayer-secrets
```

These secrets are automatically saved and injected into the relayer container through environment variables.

> [!INFO]
> If you receive any permission error, check if the `scripts/generate_keys.sh` is configured to be executable. If not, do this running `chmod +x scripts/generate_keys.sh`

Now deploy the relayer components:

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