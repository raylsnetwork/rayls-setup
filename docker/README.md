Here’s a new README for your **Docker Compose-based Rayls stack**, inspired by the original Helm/K8s deployment doc but adapted to your `.env` and `Makefile`. You can copy this into a `README.md` file:

---

# Rayls Docker Compose Deployment

This repository provides a Docker Compose setup for deploying Rayls components locally for development and testing purposes. It includes services such as MongoDB, Privacy Ledger, Relayer, KMM, and Circom API.

## Prerequisites

Ensure you have the following installed:

* [Docker](https://www.docker.com/)
* [Docker Compose](https://docs.docker.com/compose/)
* [make](https://www.gnu.org/software/make/)

## Step 1: Clone and Configure the Repository

```bash
git clone <your-repo-url>
cd <repo-folder>
```

Create the `.env` file and adjust the values to match your setup:

```bash
# The following environment variables are used to configure the Rayls application.
# Please replace the placeholder values with your actual configuration.

## Commit Chain Contracts and Privacy Ledger Contracts
NODE_CC_CHAIN_ID=1911
RPC_URL_NODE_CC=https://commitchain.parfin.io
WS_URL_NODE_CC=ws://commitchain.parfin.io

## Deploy Privacy Ledger Contracts
COMMITCHAIN_CCDEPLOYMENTPROXYREGISTRY=0xB42058A1cD0593352d53EA54c24545F2a0bD4131
# Use the command `make create-private-key` to generate the private key
PRIVATE_KEY_SYSTEM=0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef
RPC_URL_NODE_PL=http://privacy-ledger:8545
NODE_PL_CHAIN_ID=191100

# Database variables
MONGODB_CONNECTION_STRING="mongodb://mongodb:27017/admin?directConnection=true&replicaSet=rs0"

# Relayer variables
BLOCKCHAIN_DATABASE_NAME=rayls-relayer
BLOCKCHAIN_KMS_OPERATION_SERVICE_ROOT_URL=http://kmm:8083
BLOCKCHAIN_ENYGMA_PROOF_API_ADDRESS=http://circom-api:3000
BLOCKCHAIN_PLSTARTINGBLOCK=100
BLOCKCHAIN_EXECUTOR_BATCH_MESSAGES=500
BLOCKCHAIN_PLENDPOINTADDRESS=0x3333333333333333333333333333333333333333
BLOCKCHAIN_LISTENER_BATCH_BLOCKS=50
BLOCKCHAIN_STORAGE_PROOF_BATCH_MESSAGES=200
BLOCKCHAIN_ENYGMA_PL_EVENTS=0x4444444444444444444444444444444444444444
COMMITCHAIN_CCSTARTINGBLOCK=2334810
COMMITCHAIN_ATOMICREVERTSTARTINGBLOCK=2334810
KMS_DATABASE_NAME=rayls-kmm
# Use the command `make create-relayer-secrets` to generate the secrets
BLOCKCHAIN_KMS_API_KEY=exampleapikey1234567890
BLOCKCHAIN_KMS_SECRET=exampleapisecret1234567890abcdef1234567890abcdef
KMS_API_KEY=exampleapikey1234567890
KMS_SECRET=exampleapisecret1234567890abcdef1234567890abcdef
```

Make sure the `.env` file includes the required environment variables. You can generate private keys and relayer secrets using the provided `make` commands.

This command wraps `docker compose up -d --build` and will initialize all containers.

## Step 2: Initialize MongoDB Replica Set

After MongoDB is up, initialize the replica set:

```bash
make mongodb up
```

## Step 3: Initialize Privacy Ledger

After MongoDB is up, initialize the replica set:

```bash
make pivacy-ledger up
```

## Step 4: Deploy Privacy Ledger Contracts

Before continuing, ensure the Privacy Ledger service is healthy. Then run:

```bash
make deploy-privacy-ledger
```

This uses a containerized deployment script to deploy smart contracts on the Privacy Ledger node defined in `.env`.

> ⚠️ **Save the contract addresses output.** These values must be added back to `.env`.

## Step 5: Generate Relayer Secrets

Run the following command to generate KMS and relayer secrets:

```bash
make create-relayer-secrets
```

These secrets are automatically saved and injected into the relayer container through environment variables.

Now deploy the relayer components:

```bash
make relayer
```

## Step 6: Verify Services

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
docker ps
docker exec -it <container_name> sh
```

## Environment Variables

See `.env` file for a full list of configuration options. Key variables include:

* `RPC_URL_NODE_PL`, `RPC_URL_NODE_CC`: URLs for PL and CC nodes.
* `MONGODB_CONNECTION_STRING`: MongoDB replica set connection string.
* `BLOCKCHAIN_KMS_SECRET`, `KMS_SECRET`: Secrets used for signing and encryption.
* `BLOCKCHAIN_PLSTARTINGBLOCK`, `COMMITCHAIN_CCSTARTINGBLOCK`: Initial block numbers for indexers.
