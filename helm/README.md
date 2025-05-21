# Rayls Deployment

This repository contains Helm charts and deployment scripts for setting up a Privacy Ledger, deploying smart contracts, and launching the Relayer services.

## Prerequisites

Before deploying the system, ensure you have the following installed and configured:

- [Docker](https://www.docker.com/) – for running and testing images locally.
- [Helm](https://helm.sh/) (v3 or later) – package manager for Kubernetes.
- [kubectl](https://kubernetes.io/docs/tasks/tools/) – to interact with your Kubernetes cluster.
- [make](https://www.gnu.org/software/make/) - to interact with our scripts.

## Step 1: Deploy MongoDB

Both Rayls Privacy Ledger and Rayls Relayer require a MongoDB cluster with a configured Replica Set.
If you don't have a MongoDB installation with Replica Set, and cannot install one or use MongoDB Atlas, Parfin provides a ready-to-use container image of MongoDB 6 with ReplicaSet.

This image is available at:
`public.ecr.aws/rayls/rayls-mongors`

Here is the translated version of your warning and installation instructions:

---

⚠️ **Warning:**

Please note that this image will only be available in the repository **for the duration of the Drex testing period**, and **must not be used in production environments**.
Parfin does **not provide support** for MongoDB or assume any responsibility for **data loss** related to the use of this image.

⚠️ **Important:** The MongoDB volume is configured using the **HostPath plugin** for testing purposes.
If the host is destroyed, **all data will be lost**.

To install MongoDB using the image provided by Parfin, run:

```bash
kubectl create namespace rayls
helm install mongodb charts/mongodb -n rayls
```

## Step 2: Deploy Privacy Ledger

The Privacy Ledger is a required component and must be deployed before deploying smart contracts or the relayer.

### 2.1. Clone the Repository

```bash
git clone <public-repo>
cd privacy-ledger
```

### 2.2. Configure MongoDB Connection
Simply provide a valid connection string in the MONGODB_CONN environment variable inside charts/privacy-ledger/values.yaml:

```yaml
env:
  MONGODB_CONN: "mongodb://<user>:<password>@<host>:<port>/<db>?authSource=admin"
```

### 2.3. Deploy Privacy Ledger

⚠️ Important: The volume used by Privacy Ledger is configured with the HostPath plugin for testing purposes.
If the host is destroyed, all data will be lost.


```bash
helm upgrade --install -n rayls privacy-ledger charts/privacy-ledger
```

## Step 3: Deploy Smart Contracts

Smart contracts are deployed using a pre-built Docker image:
`public.ecr.aws/rayls/rayls-contracts-privacy-ledger`

This step depends on a running Privacy Ledger instance.

Create a .env:
```
## Deploy Privacy Ledger Contracts
COMMITCHAIN_CCDEPLOYMENTPROXYREGISTRY=0xB42058A1cD0593352d53EA54c24545F2a0bD4131
PRIVATE_KEY_SYSTEM=example_private_key # Use the command `make create-private-key` to generate the private key
RPC_URL_NODE_PL=https://commitchain.parfin.io
NODE_PL_CHAIN_ID=191100
```

Now, deploy the contracts to the privacy-ledger:
```
docker pull public.ecr.aws/rayls/rayls-contracts-privacy-ledger:v2.3.1
docker run public.ecr.aws/rayls/rayls-contracts-privacy-ledger:v2.3.1 --network shared_cc
```

```sh
make deploy-privacy-ledger
```

**Save this output, it's extremelly important**

```
The file unknown-<NODE_PL_CHAIN_ID>.json will be automatically generated in the ./contracts/.openzeppelin directory during deployment.
It is important that this file is version-controlled (committed to the repository) at the end of the process.
```

---

## Step 4: Deploy Relayer, KMM, Atomic Service, and Circom API

After deploying the Governance API and the Privacy Ledger, you can proceed with the remaining components.

### 4.1. Create Relayer Secrets

Run the following script to generate the necessary keys for the relayer:

```bash
make create-relayer-secrets
```

### 4.2. Configure Relayer Environment

Update the environment variables in `./charts/relayer/values.yaml` as needed:

```yaml
env:
  BLOCKCHAIN_DATABASE_TYPE: "mongodb"
  BLOCKCHAIN_KMS_OPERATION_SERVICE_ROOT_URL: "http://<relayer-release>-kmm-svc:8080"
  BLOCKCHAIN_CHAINID: 600123
  BLOCKCHAIN_CHAINURL: "http://<release-privacy-ledger>-svc:8545"
  BLOCKCHAIN_PLSTARTINGBLOCK: "0"
  BLOCKCHAIN_EXECUTOR_BATCH_MESSAGES: "500"
  BLOCKCHAIN_PLENDPOINTADDRESS: "0x0000000000000000000000000000000000000000"
  BLOCKCHAIN_LISTENER_BATCH_BLOCKS: "50"
  BLOCKCHAIN_STORAGE_PROOF_BATCH_MESSAGES: "200"
  BLOCKCHAIN_ENYGMA_PROOF_API_ADDRESS: "http://<relayer-release>-circomapi-svc:3000"
  BLOCKCHAIN_ENYGMA_PL_EVENTS: "0x0000000000000000000000000000000000000000"
  BLOCKCHAIN_DATABASE_CONNECTIONSTRING: "mongodb://<mongodb-release-name>.<namespace>.svc.cluster.local:27017/admin?directConnection=true&replicaSet=rs0"
  COMMITCHAIN_CHAINURL: "https://commitchain.parfin.io"
  COMMITCHAIN_VERSION: "2.0"
  COMMITCHAIN_CHAINID: "999990001"
  COMMITCHAIN_CCSTARTINGBLOCK: "1990335"
  COMMITCHAIN_ATOMICREVERTSTARTINGBLOCK: "1990335"
  COMMITCHAIN_OPERATORCHAINID: "999"
  COMMITCHAIN_CCDEPLOYMENTPROXYREGISTRY: "0x9bfe7a23fC8882D7A692d959C89c0c2A7266bfED"
  COMMITCHAIN_CCENDPOINTMAXBATCHMESSAGES: "500"
  COMMITCHAIN_EXPIRATIONREVERTTIMEINMINUTES: "30"
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
  KMS_DATABASE_CONNECTIONSTRING: "mongodb://<mongodb-release-name>.<namespace>.svc.cluster.local:27017/admin?directConnection=true&replicaSet=rs0"
  BLOCKCHAIN_KMS_API_KEY: "bc02718914e14e20f58f1a7fb8e042f8"
  BLOCKCHAIN_KMS_SECRET: "a0b25b23605d2f8ca7cb418838a1cddf40c9626682b4b19df3ed245681cc6a5a"
  KMS_API_KEY: "bc02718914e14e20f58f1a7fb8e042f8"
  KMS_SECRET: "a0b25b23605d2f8ca7cb418838a1cddf40c9626682b4b19df3ed245681cc6a5a"
```

> Make sure to replace placeholders like `<relayer-release>`, `<release-privacy-ledger>`, and `<mongodb-release-name>` with your actual Helm release and namespace names.

### 4.3. Deploy Relayer with Helm

```bash
helm install relayer charts/relayer -n <namespace>
```