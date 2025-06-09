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
We are providing here a `.env` file that includes the required environment variables. Please change the value where have a comment `# Change this`

You can generate private keys and relayer secrets using the provided `make` commands.

The KMS env values, are only to be used if the KMM module for KMS is enabled. If you are not encrypting the keys that are stored in the database, you do not need to change the `xxx` values.

## Step 2: Initialize MongoDB Replica Set

For test purposes, we are provinding a containers that initializes a MongoDB cluster with a Replica Set, which is required for the application.

To initialize the MongoDB with the replica set, just run:

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
