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
git clone git@github.com:raylsnetwork/rayls-setup.git
cd rayls-setup/docker
```

Create the `.env` file and adjust the values to match your setup:

```bash
PRIVATE_KEY_SYSTEM=0x3f8e605eea31dfbe118a34391876caf619702f6b4f39dd7665db4ca7609322cb
NODE_CC_CHAIN_ID=1911
COMMITCHAIN_CCDEPLOYMENTPROXYREGISTRY=0xc2BaA3D18EE3B9A2425Bd5a8018e3F2f1171cDd2

RPC_URL_NODE_CC=https://commitchain.parfin.io

## Add Participant
PARTICIPANT_CHAIN_ID=000000 # Change this to your NetworkID
PARTICIPANT_NAME=PL_NAME
# 0: Participant, 1: Issuer, 2: Auditor
PARTICIPANT_ROLE=1
PARTICIPANT_OWNER_ADDRESS=0x0000000000000000000000000000000000000000

# Database variables
MONGODB_CONNECTION_STRING="mongodb://mongodb:27017/admin?directConnection=true&replicaSet=rs0"

# Relayer variables
BLOCKCHAIN_DATABASE_TYPE: "mongodb"
BLOCKCHAIN_KMS_OPERATION_SERVICE_ROOT_URL: "http://kmm:8080"
BLOCKCHAIN_CHAINID: 600123
BLOCKCHAIN_CHAINURL: "http://<release-privacy-ledger>-svc:8545"
BLOCKCHAIN_PLSTARTINGBLOCK: "0"
BLOCKCHAIN_EXECUTOR_BATCH_MESSAGES: "500"
BLOCKCHAIN_PLENDPOINTADDRESS: "0x0000000000000000000000000000000000000000"
BLOCKCHAIN_LISTENER_BATCH_BLOCKS: "50"
BLOCKCHAIN_STORAGE_PROOF_BATCH_MESSAGES: "200"
BLOCKCHAIN_ENYGMA_PROOF_API_ADDRESS: "http://circomapi:3000"
BLOCKCHAIN_ENYGMA_PL_EVENTS: "0x0000000000000000000000000000000000000000"
BLOCKCHAIN_DATABASE_CONNECTIONSTRING: "mongodb://mongodb:27017/admin?directConnection=true&replicaSet=rs0"
COMMITCHAIN_CHAINURL: "https://commitchain.parfin.io"
COMMITCHAIN_VERSION: "2.0"
COMMITCHAIN_CHAINID: "999990001"
COMMITCHAIN_CCSTARTINGBLOCK: "1990335"
COMMITCHAIN_ATOMICREVERTSTARTINGBLOCK: "1990335"
COMMITCHAIN_OPERATORCHAINID: "999"
COMMITCHAIN_CCDEPLOYMENTPROXYREGISTRY: "0x9bfe7a23fC8882D7A692d959C89c0c2A7266bfED"
COMMITCHAIN_CCENDPOINTMAXBATCHMESSAGES: "500"
COMMITCHAIN_EXPIRATIONREVERTTIMEINMINUTES: "30"
COMMITCHAIN_ZKDVPMERKLETREEDEPTH: "8"
COMMITCHAIN_BLOCKTIME_INSECONDS: "5"
LOG_LEVEL: "Info"
LOG_HANDLER: "Text"
KMS_CORSDOMAIN: "*"
KMS_AWSPROFILE: "xxx"
KMS_AWSALIAS: "xxx"
KMS_GCPPROJECT: "xxx"
KMS_GCPLOCATION: "xxx"
KMS_GCPKEYRING: "xxx"
KMS_GCPCRYPTOKEY: "xxx"
KMS_ENCRYPTORSERVICE: "plaintext"
KMS_DATABASE_CONNECTIONSTRING: "mongodb://mongodb:27017/admin?directConnection=true&replicaSet=rs0"
BLOCKCHAIN_KMS_API_KEY: "bc02718914e14e20f58f1a7fb8e042f8"
BLOCKCHAIN_KMS_SECRET: "a0b25b23605d2f8ca7cb418838a1cddf40c9626682b4b19df3ed245681cc6a5a"
KMS_API_KEY: "bc02718914e14e20f58f1a7fb8e042f8"
KMS_SECRET: "a0b25b23605d2f8ca7cb418838a1cddf40c9626682b4b19df3ed245681cc6a5a"
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
make privacy-ledger up
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
