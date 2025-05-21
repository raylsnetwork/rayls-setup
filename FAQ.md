# Frequently Asked Questions

## What is the expected output when I deploy my Privacy Ledger?

The output will be similar to this:

```
Starting deployment of Private Ledger base contracts...
Deployer Address: 0x0000000000000000000000000000000000000000
###########################################
🛠️ DEPLOYMENT_REGISTRY_ADDRESS_PL not found in .env file. Deploying a new DeploymentRegistry contract...
Deploying DeploymentRegistry...
✅ DeploymentRegistry deployed at 0x0000000000000000000000000000000000000000
Deploying RaylsMessageExecutorV1...
✅ RaylsMessageExecutorV1 deployed at 0x0000000000000000000000000000000000000000
Deploying EndpointV1...
✅ EndpointV1 deployed at 0x0000000000000000000000000000000000000000
Deploying RaylsContractFactoryV1...
✅ RaylsContractFactoryV1 deployed at 0x0000000000000000000000000000000000000000
Deploying ParticipantStorageReplicaV1...
✅ ParticipantStorageReplicaV2 deployed at 0x0000000000000000000000000000000000000000
Deploying TokenRegistryReplicaV1...
✅ TokenRegistryReplicaV1 deployed at 0x0000000000000000000000000000000000000000
Deploying EnygmaPLEvent...
✅ EnygmaPLEvent deployed at0x0000000000000000000000000000000000000000
✅ Finished deployment of PL base contracts
===========================================
👉 Contract Addresses 👈
RAYLS_MESSAGE_EXECUTOR: 0x0000000000000000000000000000000000000000
PL_ENDPOINT: 0x0000000000000000000000000000000000000000
RAYLS_CONTRACT_FACTORY: 0x0000000000000000000000000000000000000000
PARTICIPANT_STORAGE_REPLICA: 0x0000000000000000000000000000000000000000
ENYGMA_PL_EVENTS:0x0000000000000000000000000000000000000000
-------------------------------------------
Configuring contracts in EndpointV1...
✅ Contracts configured successfully in EndpointV1.
Synchronizing participant data from Commit Chain...
✅ Participant data synchronization complete.
Synchronizing frozen tokens from Commit Chain...
✅ Frozen tokens synchronization complete.
Registering Token Registry in EndpointV1...
✅ Token Registry registered successfully.
Saving deployment data for version: 2.0
⏳ Waiting for transaction to be mined...
✅ Deployment data saved on blockchain!
✅ Deployment data saved for version 2.0

NODE_PL_ENDPOINT_ADDRESS0x0000000000000000000000000000000000000000

===========================================
👉👉👉👉 Relayer Configuration 👈👈👈👈
-------------------------------------------
ENV FORMAT:

BLOCKCHAIN_PLSTARTINGBLOCK=00
BLOCKCHAIN_EXECUTOR_BATCH_MESSAGES=500
BLOCKCHAIN_PLENDPOINTADDRESS=0x00000000000000000000000000000000000000003
BLOCKCHAIN_LISTENER_BATCH_BLOCKS=50
BLOCKCHAIN_STORAGE_PROOF_BATCH_MESSAGES=200
BLOCKCHAIN_ENYGMA_PL_EVENTS=0x0000000000000000000000000000000000000000
```

## What is the expected output when I deploy the Relayer?

Expected output:

```
relayer-1  | [21:11:50 2025-04-15] INFO: Configuration validated successfully | 
relayer-1  | [21:11:50 2025-04-15] INFO: Initializing.. | CCEndpointMaxBatchMessages=500 
relayer-1  | Working dir path:  /app
relayer-1  | Migrations path:  file:///app/database/mongodb/migrations
relayer-1  | [21:11:50 2025-04-15] INFO: No migration files found. Skipping migration. | 
relayer-1  | [21:11:52 2025-04-15] INFO: Getting DH pair | 
relayer-1  | [21:11:52 2025-04-15] WARN: DH pair not found |  
relayer-1  | [21:11:52 2025-04-15] INFO: Creating DH pair |  
relayer-1  | [21:11:52 2025-04-15] INFO: Successfully created and retrieved DH pair | 
relayer-1  | [21:11:52 2025-04-15] INFO: Retrieving enygma key | 
relayer-1  | [21:11:52 2025-04-15] WARN: Enygma key not found | 
relayer-1  | [21:11:52 2025-04-15] INFO: Creating enygma key | 
relayer-1  | [21:11:52 2025-04-15] INFO: Successfully created enygma key | 
relayer-1  | [21:11:52 2025-04-15] INFO: Retrieving relayer ECDSA keys | 
relayer-1  | [21:11:52 2025-04-15] WARN: Relayer ECDSA keys not found | 
relayer-1  | [21:11:52 2025-04-15] INFO: Creating relayer ECDSA keys | 
relayer-1  | [21:11:52 2025-04-15] INFO: Successfully created relayer ECDSA keys | 
relayer-1  | [21:11:52 2025-04-15] INFO: Initialising private Ledger starting block number | StartingBlock=00 
relayer-1  | [21:11:52 2025-04-15] INFO: Initialising Commit Chain starting block from config | StartingBlock=123456
relayer-1  | [21:11:53 2025-04-15] INFO: Private keys for PL and CC populated successfully | 
relayer-1  | [21:11:53 2025-04-15] INFO: DH public key already registered | ChainId=123456789
relayer-1  | [21:11:54 2025-04-15] INFO: BabyJubjub X & Y keys already registered | ChainId=123456789
relayer-1  | [21:11:54 2025-04-15] INFO: Chain ID already registered | ChainId=123456789 
relayer-1  | [21:11:54 2025-04-15] INFO: Audit info already registered | ChainId=123456789 
relayer-1  | [21:11:56 2025-04-15] INFO: Adding logs to CC batcher | Batch length:=2 
relayer-1  | [21:11:57 2025-04-15] INFO: Total messages to finishEIP5164Transaction | Total Messages=0 
relayer-1  | [21:11:57 2025-04-15] INFO: Total messages to execute on PL from CC | Total Messages=2 
relayer-1  | [21:11:57 2025-04-15] INFO: Messages for the PL from the CC executed | Total Executed Messages:=2 
```