# Parity Protocol Distributed System Deployment Guide

This guide walks you through setting up and running the complete Parity Protocol distributed system with real blockchain transactions, federated learning, and reputation management.

## ⚠️ IMPORTANT: Real Blockchain Transactions

**This system uses REAL blockchain transactions on the Filecoin Calibration Network. All transfers, staking, and reward distributions are actual on-chain transactions that cost real USDFC tokens.**

## Prerequisites

- Go 1.19+
- Docker and Docker Compose
- PostgreSQL
- Ollama (for LLM inference)
- **Private key with USDFC tokens on Filecoin Calibration Network**
- **Staked tokens for participation**

## 1. Blockchain Setup

### Required Smart Contracts

The system uses these deployed smart contracts on Filecoin Calibration Network:

- **RunnerReputation Contract**: Manages staking, reputation, and slashing
- **USDFC Token Contract**: ERC-20 stablecoin for payments and staking
- **Stake Wallet Contract**: Handles payment distributions

### Staking Requirement

Before participating, users must:

1. Hold USDFC tokens
2. Stake minimum required amount in the RunnerReputation contract
3. Maintain good reputation to avoid slashing

## 2. Database Setup

### Start PostgreSQL

```bash
# Using Docker
docker run --name parity-postgres \
  -e POSTGRES_DB=parity \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=password \
  -p 5432:5432 \
  -d postgres:14

# Or use existing PostgreSQL installation
```

### Database Migration

The system will auto-migrate tables on startup, including:

- `tasks` and `task_results`
- `runners` and `runner_reputation`
- `prompt_requests` and `model_capabilities`
- `federated_learning_sessions`, `fl_rounds`, `fl_participants`
- `billing_metrics`

## 3. Ollama Setup (LLM Runtime)

### Install Ollama

```bash
# macOS
brew install ollama

# Linux
curl -fsSL https://ollama.ai/install.sh | sh

# Windows - Download from ollama.ai
```

### Start Ollama Service

```bash
# Start Ollama server
ollama serve

# In another terminal, pull some models
ollama pull llama2
ollama pull mistral
ollama pull codellama
```

Ollama will run on `http://localhost:11434` by default.

## 4. Start Parity Server

```bash
cd parity-server

# Configure environment
cp .env.sample .env
# Edit .env with your blockchain settings:
# DATABASE_URL=postgres://postgres:password@localhost:5432/parity
# SERVER_HOST=localhost
# SERVER_PORT=8080
# FILECOIN_RPC=https://api.calibration.node.glif.io/rpc/v1
# FILECOIN_CHAIN_ID=314159
# USDFC_TOKEN_ADDRESS=0x...
# REPUTATION_CONTRACT_ADDRESS=0x...
# STAKE_WALLET_ADDRESS=0x...

# First-time setup - authenticate with your private key
./parity-server auth --private-key YOUR_PRIVATE_KEY

# Build and run
make build
./parity-server server

# Or run directly
go run cmd/main.go server
```

The server will start on `http://localhost:8080` and provide endpoints:

- **Task Management**: `POST /api/v1/tasks`, `GET /api/v1/tasks`
- **LLM Processing**: `POST /api/v1/llm/prompts`, `GET /api/v1/llm/prompts/{id}`
- **Federated Learning**: `POST /api/v1/federated-learning/sessions`, `GET /api/v1/federated-learning/sessions`
- **Reputation System**: `GET /api/v1/reputation/stats`, `POST /api/v1/reputation/reports`
- **Runner Management**: `GET /api/v1/runners`, `POST /api/v1/runners/register`

## 5. Start Parity Runner

### Authenticate Runner

```bash
cd parity-runner

# First-time setup - authenticate with a private key
./parity-runner auth --private-key YOUR_PRIVATE_KEY

# Ensure you have staked USDFC tokens before running tasks
```

### Configure Runner Environment

```bash
cp .env.sample .env
# Edit .env:
# SERVER_URL=http://localhost:8080
# OLLAMA_URL=http://localhost:11434
# WEBHOOK_PORT=8081
```

### Start Runner

```bash
# Run the runner service
./parity-runner runner

# Or run directly
go run cmd/main.go runner
```

The runner will:

- Register itself with the server using real blockchain identity
- Discover available Ollama models
- Listen for task assignments via webhooks
- Execute federated learning training
- Receive real USDFC token rewards for completed tasks

## 6. Client Usage Examples

### CLI Usage

```bash
cd parity-client

# Build client
make build

# Submit a prompt
./parity-client llm submit \
  --prompt "Explain quantum computing" \
  --model "llama2" \
  --creator-address "0x123..." \
  --client-id "my-app"

# Create federated learning session
./parity-client fl create-session \
  --name "Image Classification" \
  --model-type neural_network \
  --dataset-cid QmXXX... \
  --total-rounds 10 \
  --min-participants 3 \
  --aggregation-method fedavg \
  --learning-rate 0.001 \
  --batch-size 32 \
  --local-epochs 5 \
  --config-file model_config.json

# Check reputation status
./parity-client reputation status --runner-id YOUR_DEVICE_ID

# Get billing metrics
./parity-client llm billing --client-id "my-app"
```

### Federated Learning Model Configuration

Create a `model_config.json` file:

```json
{
  "hidden_size": 128,
  "architecture": {
    "input_dim": 784,
    "output_dim": 10,
    "activation": "relu",
    "output_activation": "softmax"
  },
  "optimizer": {
    "type": "sgd",
    "momentum": 0.9,
    "weight_decay": 0.0001
  },
  "loss": "cross_entropy",
  "metrics": ["accuracy"]
}
```

## 7. System Features

### Real Blockchain Integration

- **Staking**: Users must stake USDFC tokens to participate
- **Payments**: All task rewards are real blockchain transactions in USDFC
- **Reputation**: On-chain reputation tracking with slashing mechanisms
- **Governance**: Token-based governance for system parameters

### Federated Learning

- **Distributed Training**: Multi-party machine learning without data sharing
- **Model Aggregation**: FedAvg, FedProx, and other aggregation methods
- **Privacy**: Differential privacy and secure aggregation support
- **Data Partitioning**: Non-IID, label skew, and other partitioning strategies

### Runner Monitoring

- **Peer-to-peer Monitoring**: Runners monitor each other for malicious behavior
- **Automatic Slashing**: Bad actors lose staked tokens
- **Performance Tracking**: Quality and reliability scoring
- **Dynamic Assignments**: Automatic monitoring task distribution

### LLM Inference

- **Model Discovery**: Automatic detection of available models
- **Load Balancing**: Intelligent task distribution
- **Billing**: Usage-based billing with token payments

## 8. Troubleshooting

### Common Issues

1. **"Device not registered" error**: Ensure you have staked USDFC tokens
2. **Transaction failures**: Check your USDFC token balance
3. **Connection issues**: Verify Filecoin RPC endpoint is accessible
4. **Authentication errors**: Ensure private key is properly set

### Monitoring

- Check server logs for blockchain transaction confirmations
- Monitor reputation scores to avoid slashing
- Verify sufficient stake balance for task creation
- Watch federated learning session progress

## 9. Security Considerations

- **Private Key Security**: Never share your private keys
- **Stake Management**: Monitor your stake to avoid slashing
- **Network Security**: Use secure RPC endpoints
- **Data Privacy**: Understand federated learning privacy guarantees

## 10. Support

For issues and questions:

- Check system logs for error details
- Verify blockchain connectivity
- Ensure sufficient token balances
- Review smart contract interactions
