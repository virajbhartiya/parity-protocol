# Parity Protocol Federated Learning Guide

## Overview

Parity Protocol implements a fully distributed federated learning system that enables collaborative machine learning without centralizing sensitive data. The system coordinates training across multiple runners while maintaining data privacy and security.

## 🧠 How Federated Learning Works

### Architecture

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Client    │    │   Server    │    │   Runner    │
│             │    │             │    │             │
│ Creates FL  │───▶│ Coordinates │───▶│ Trains on   │
│ Session     │    │ Training    │    │ Local Data  │
│             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
       ▲                   │                   │
       │                   ▼                   ▼
       │            ┌─────────────┐    ┌─────────────┐
       │            │ Aggregates  │    │ Submits     │
       └────────────│ Updates     │◀───│ Gradients   │
                    │             │    │             │
                    └─────────────┘    └─────────────┘
```

### Core Components

1. **FL Session**: Defines training parameters and coordination logic
2. **Data Partitioning**: Distributes dataset across runners
3. **Local Training**: Each runner trains on their data partition
4. **Model Aggregation**: Server combines updates using federated averaging
5. **Reward Distribution**: USDFC tokens distributed based on contribution

## 🚀 Getting Started

### Prerequisites

- Parity Protocol server running with blockchain integration
- Multiple runners registered and staked with USDFC tokens
- Training dataset uploaded to IPFS/Filecoin
- Model configuration file

### Step 1: Prepare Your Dataset

Upload your dataset to IPFS/Filecoin:

```bash
# Using the client to upload data and create session
./parity-client fl create-session-with-data ./my_dataset.csv \
    --name "Image Classification Training" \
    --model-type neural_network \
    --total-rounds 10 \
    --config-file model_config.json
```

### Step 2: Create Model Configuration

Create a `model_config.json` file:

```json
{
  "architecture": {
    "type": "neural_network",
    "layers": [
      { "type": "dense", "units": 64, "activation": "relu" },
      { "type": "dropout", "rate": 0.2 },
      { "type": "dense", "units": 32, "activation": "relu" },
      { "type": "dense", "units": 3, "activation": "softmax" }
    ]
  },
  "input_size": 5,
  "output_size": 3,
  "hidden_size": 64
}
```

### Step 3: Create Federated Learning Session

```bash
./parity-client fl create-session \
    --name "My FL Training" \
    --description "Distributed neural network training" \
    --model-type neural_network \
    --dataset-cid QmYourDatasetCID \
    --total-rounds 50 \
    --min-participants 2 \
    --split-strategy "non_iid" \
    --alpha 0.1 \
    --min-samples 100 \
    --overlap-ratio 0.0 \
    --learning-rate 0.001 \
    --batch-size 16 \
    --local-epochs 5 \
    --aggregation-method "fedavg" \
    --config-file model_config.json
```

### Step 4: Start Training

```bash
# Start the session
./parity-client fl start-session <session-id>

# Monitor progress
./parity-client fl get-session <session-id>

# List all sessions
./parity-client fl list-sessions
```

### Step 5: Retrieve Trained Model

```bash
# Get the final model
./parity-client fl get-model <session-id> --output trained_model.json
```

## 📊 Data Distribution Strategies

### Random Distribution

- **Use case**: Balanced datasets with similar feature distributions
- **Command**: `--split-strategy random`
- **Best for**: Testing and simple scenarios

### Non-IID Distribution

- **Use case**: Realistic federated scenarios with heterogeneous data
- **Command**: `--split-strategy non_iid --alpha 0.1`
- **Alpha parameter**: Lower values = more heterogeneous (0.1 = very diverse, 1.0 = more uniform)

### Stratified Distribution

- **Use case**: Maintaining class balance across participants
- **Command**: `--split-strategy stratified`
- **Best for**: Classification tasks with imbalanced classes

### Label Skew

- **Use case**: Each participant has data from only a subset of classes
- **Command**: `--split-strategy label_skew`
- **Best for**: Simulating real-world federated scenarios

## 🔧 Advanced Configuration

### Differential Privacy

Enable privacy-preserving training:

```bash
./parity-client fl create-session \
    --enable-differential-privacy \
    --noise-multiplier 1.0 \
    --l2-norm-clip 1.0 \
    # ... other parameters
```

### Custom Training Parameters

```bash
# Fine-tune training behavior
--learning-rate 0.001        # Learning rate for local training
--batch-size 32              # Batch size for training
--local-epochs 10            # Epochs per round per participant
--total-rounds 100           # Total federated rounds
--min-participants 5         # Minimum runners required
```

### Data Overlap Configuration

```bash
# Allow some data overlap between participants
--overlap-ratio 0.1          # 10% overlap between participants
--min-samples 50             # Minimum samples per participant
```

## 🏃‍♂️ How Runners Participate

### Automatic Participation

Runners automatically receive training tasks when:

1. They are online and registered
2. Have sufficient USDFC stake
3. Meet the session requirements

### Manual Task Submission

Runners can also manually submit updates:

```bash
./parity-client fl submit-update \
    --session-id <session-id> \
    --round-id <round-id> \
    --runner-id <runner-id> \
    --gradients-file gradients.json \
    --data-size 1000 \
    --loss 0.25 \
    --accuracy 0.89
```

## 🔄 Training Process Flow

### 1. Session Creation

- Client creates FL session with parameters
- Server validates configuration
- Session stored in database

### 2. Runner Assignment

- Server identifies online runners
- Assigns data partitions to each runner
- Creates training tasks

### 3. Local Training

- Runner downloads dataset partition
- Loads global model (if available)
- Trains model locally for specified epochs
- Computes gradients and metrics

### 4. Update Submission

- Runner submits gradients to server
- Includes training metrics (loss, accuracy)
- Server validates submission

### 5. Aggregation

- Server collects all updates for the round
- Performs federated averaging
- Updates global model
- Starts next round or completes session

### 6. Reward Distribution

- USDFC tokens distributed based on contribution
- Factors: data size, training quality, completion time
- Automatic blockchain transactions

## 📈 Monitoring and Analytics

### Session Status

```bash
# Get detailed session information
./parity-client fl get-session <session-id>
```

Example output:

```
Session ID:       abc123...
Name:             Image Classification
Status:           active
Current Round:    5/10
Participants:     3
Average Loss:     0.234
Average Accuracy: 0.876
```

### Training Metrics

The system tracks:

- **Loss convergence** across rounds
- **Accuracy improvement** over time
- **Participant contributions** (data size, quality)
- **Training time** per round
- **Communication overhead**

## 🔐 Security and Privacy

### Data Privacy

- Data never leaves runner devices
- Only gradients are shared
- Optional differential privacy
- Secure aggregation protocols

### Economic Security

- Stake-based participation
- Reputation scoring
- Slashing for malicious behavior
- Quality-based rewards

### Communication Security

- TLS encryption for all communications
- Authenticated API endpoints
- Blockchain-verified transactions

## 🛠 Troubleshooting

### Common Issues

1. **No available runners**

   ```
   Error: insufficient online runners
   Solution: Ensure runners are online and staked
   ```

2. **Training convergence issues**

   ```
   Problem: Loss not decreasing
   Solutions:
   - Reduce learning rate
   - Increase local epochs
   - Check data quality
   ```

3. **Memory issues**
   ```
   Problem: OOM during training
   Solutions:
   - Reduce batch size
   - Simplify model architecture
   - Increase runner memory limits
   ```

### Performance Optimization

1. **Faster Convergence**

   - Use stratified data splitting
   - Increase local epochs
   - Tune learning rate

2. **Better Accuracy**

   - Increase total rounds
   - Use larger models
   - Improve data quality

3. **Reduced Communication**
   - Gradient compression
   - Selective updates
   - Adaptive round timing

## 🧪 Example Scenarios

### Scenario 1: Simple Classification

```bash
# Create a basic neural network for iris classification
./parity-client fl create-session \
    --name "Iris Classification" \
    --model-type neural_network \
    --dataset-cid QmIrisDataset \
    --total-rounds 20 \
    --min-participants 2 \
    --split-strategy stratified \
    --learning-rate 0.01 \
    --batch-size 16 \
    --local-epochs 5 \
    --aggregation-method fedavg \
    --config-file iris_config.json
```

### Scenario 2: Privacy-Preserving Training

```bash
# Train with differential privacy
./parity-client fl create-session \
    --name "Private Medical Data" \
    --model-type neural_network \
    --dataset-cid QmMedicalDataset \
    --enable-differential-privacy \
    --noise-multiplier 0.5 \
    --l2-norm-clip 1.0 \
    --split-strategy non_iid \
    --alpha 0.1 \
    --total-rounds 100 \
    --config-file medical_config.json
```

### Scenario 3: Heterogeneous Data

```bash
# Simulate real-world federated scenario
./parity-client fl create-session \
    --name "Mobile Device Training" \
    --model-type neural_network \
    --dataset-cid QmMobileDataset \
    --split-strategy label_skew \
    --min-participants 10 \
    --overlap-ratio 0.05 \
    --total-rounds 200 \
    --learning-rate 0.001 \
    --config-file mobile_config.json
```

## 📚 Advanced Topics

### Custom Aggregation Methods

The system supports multiple aggregation strategies:

- **FedAvg**: Weighted average by data size
- **FedProx**: Proximal term for heterogeneous data
- **FedNova**: Normalized averaging
- **Custom**: Implement your own aggregation logic

### Model Architectures

Supported model types:

- **neural_network**: Multi-layer perceptrons
- **linear_regression**: Linear models
- **custom**: User-defined architectures

### Integration with Blockchain

- All rewards paid in USDFC tokens
- Stake-based security model
- On-chain reputation tracking
- Automatic payment distribution

## 🎯 Best Practices

1. **Data Preparation**

   - Clean and normalize data
   - Handle missing values
   - Balance classes when possible

2. **Model Design**

   - Start with simple architectures
   - Use appropriate activation functions
   - Include regularization (dropout, L2)

3. **Training Configuration**

   - Start with higher learning rates
   - Use adaptive learning rate schedules
   - Monitor convergence carefully

4. **Privacy Considerations**

   - Enable differential privacy for sensitive data
   - Use secure aggregation when available
   - Minimize data overlap

5. **Economic Optimization**
   - Set appropriate rewards
   - Monitor participant incentives
   - Balance quality vs. participation

This federated learning system provides a robust, privacy-preserving, and economically incentivized platform for collaborative machine learning across the Parity Protocol network.
