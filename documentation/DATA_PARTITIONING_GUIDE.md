# Parity Protocol Data Partitioning Guide

## Overview

Data partitioning is a critical component of federated learning that determines how datasets are distributed across participating runners. Parity Protocol implements multiple partitioning strategies to simulate realistic federated learning scenarios while maintaining data privacy and enabling effective collaborative training.

## 🎯 Why Data Partitioning Matters

### Federated Learning Challenges

Real-world federated learning faces several data distribution challenges:

- **Non-IID Data**: Each participant has different data distributions
- **Statistical Heterogeneity**: Varying feature distributions across devices
- **System Heterogeneity**: Different computational capabilities
- **Privacy Requirements**: Data cannot be centralized for analysis

### Parity Protocol Solution

Our partitioning system addresses these challenges by:

- Simulating realistic data heterogeneity
- Preserving privacy through local computation
- Supporting multiple distribution strategies
- Enabling controllable experimental conditions

## 📊 Partitioning Strategies

### 1. Random Partitioning

**Use Case**: Testing and baseline scenarios with balanced data

```bash
./parity-client fl create-session \
    --split-strategy "random" \
    --min-samples 100 \
    --overlap-ratio 0.0
```

**How it works**:

- Randomly shuffles the entire dataset
- Divides data into equal-sized partitions
- Each runner gets approximately the same amount of data
- Maintains overall data distribution

**Advantages**:

- Simple and fast
- Balanced workloads
- Good for initial testing

**Disadvantages**:

- Not realistic for real federated scenarios
- Doesn't simulate data heterogeneity

**Example**:

```
Dataset: [A1, A2, B1, B2, C1, C2, A3, B3, C3, A4]
Partition 1: [A1, B2, C3, A4]  # Random mix
Partition 2: [A2, C1, B3, A3]  # Random mix
Partition 3: [B1, C2]          # Random mix
```

### 2. Non-IID Partitioning (Dirichlet Distribution)

**Use Case**: Realistic federated scenarios with heterogeneous data

```bash
./parity-client fl create-session \
    --split-strategy "non_iid" \
    --alpha 0.1 \
    --min-samples 50 \
    --overlap-ratio 0.0
```

**How it works**:

- Uses Dirichlet distribution to control heterogeneity
- Alpha parameter controls the degree of non-IID-ness
- Lower alpha = more heterogeneous data
- Higher alpha = more uniform distribution

**Alpha Parameter Effects**:

- **α = 0.1**: Very heterogeneous (realistic mobile scenarios)
- **α = 0.5**: Moderately heterogeneous
- **α = 1.0**: Mildly heterogeneous
- **α = 10.0**: Nearly uniform (similar to random)

**Advantages**:

- Realistic simulation of federated scenarios
- Controllable heterogeneity level
- Better evaluation of FL algorithms

**Disadvantages**:

- More complex setup
- May create imbalanced partitions
- Requires careful alpha tuning

**Example with α = 0.1**:

```
Dataset: 1000 samples, 3 classes (A, B, C)
Partition 1: [90% A, 8% B, 2% C]    # Skewed toward A
Partition 2: [5% A, 85% B, 10% C]   # Skewed toward B
Partition 3: [15% A, 10% B, 75% C]  # Skewed toward C
```

### 3. Stratified Partitioning

**Use Case**: Maintaining class balance across all participants

```bash
./parity-client fl create-session \
    --split-strategy "stratified" \
    --min-samples 100
```

**How it works**:

- Preserves class distribution in each partition
- Each participant gets proportional representation of all classes
- Balances both data quantity and quality

**Advantages**:

- Maintains class balance
- Stable training across participants
- Prevents class imbalance issues

**Disadvantages**:

- Less realistic for real federated scenarios
- May not reflect actual data distributions
- Requires labeled data

**Example**:

```
Dataset: 300 samples, 3 classes (100 A, 100 B, 100 C)
Partition 1: [33 A, 33 B, 33 C]    # Balanced
Partition 2: [33 A, 33 B, 33 C]    # Balanced
Partition 3: [34 A, 34 B, 34 C]    # Balanced
```

This comprehensive partitioning system enables realistic federated learning experiments while maintaining the flexibility needed for diverse application scenarios in the Parity Protocol network.
