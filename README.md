# Parity Protocol

[![Go Version](https://img.shields.io/badge/Go-1.22.7%2B-00ADD8?style=flat-square&logo=go)](https://go.dev/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14.0%2B-336791?style=flat-square&logo=postgresql)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Required-2496ED?style=flat-square&logo=docker)](https://www.docker.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)

## Introduction

Parity Protocol is a cutting-edge distributed computation network that enables secure and verifiable task execution across verified nodes. Built and maintained by Blit Labs, this protocol offers a cloudless architecture that prioritizes security, reliability, and efficient resource utilization.

📚 [Read the Complete Documentation](https://blitlabs.xyz/docs)

## System Architecture

The Parity Protocol ecosystem consists of five core components, each serving a specific purpose in the network:

### Core Components

#### 🏃 [parity-runner](https://github.com/theblitlabs/parity-runner)

- Manages secure task execution in isolated Docker environments
- Provides comprehensive execution metrics and system usage analytics
- Ensures computational integrity and optimal resource allocation

#### 🖥️ [parity-server](https://github.com/theblitlabs/parity-server)

- Orchestrates task distribution and validation processes
- Handles node registration and reputation management
- Implements real-time communication via webhooks

#### 🔌 [parity-client](https://github.com/theblitlabs/parity-client)

- Delivers a seamless interface for task submission and monitoring
- Provides robust APIs for comprehensive task management
- Features real-time execution tracking and detailed analytics

#### 💎 [parity-token](https://github.com/theblitlabs/parity-token)

- Implements a custom ERC20 token for network operations
- Powers protocol economics and staking mechanisms
- Facilitates automated reward distribution

#### 👛 [parity-wallet](https://github.com/theblitlabs/parity-wallet)

- Provides secure token management capabilities
- Features enterprise-grade authentication system
- Enables efficient transaction handling

### Supporting Tools

| Tool                                                          | Purpose                                          |
| ------------------------------------------------------------- | ------------------------------------------------ |
| [gologger](https://github.com/theblitlabs/gologger)           | Advanced structured logging for Go applications  |
| [deviceid](https://github.com/theblitlabs/deviceid)           | Robust device identification system              |
| [keystore](https://github.com/theblitlabs/keystore)           | Secure cryptographic key management              |
| [go-wallet-sdk](https://github.com/theblitlabs/go-wallet-sdk) | Comprehensive Go SDK for blockchain interactions |

## Getting Started

### System Requirements

Before beginning, ensure your system meets these prerequisites:

- **Go**: Version 1.22.7 or higher
- **PostgreSQL**: Version 14.0 or higher
- **Docker**: Latest stable version
- **Make**: Latest version
- **Git**: Latest version

### Installation Guide

#### 1. Repository Setup

```bash
mkdir parity-workspace && cd parity-workspace

# Clone core repositories
for repo in server runner client token wallet; do
    git clone https://github.com/theblitlabs/parity-${repo}.git
done
```

#### 2. Initial Configuration

```bash
# Set up token and wallet first
cd parity-token
# Follow README instructions for:
# 1. Token contract deployment
# 2. Parameter configuration
# 3. Initial distribution setup

cd ../parity-wallet
# Follow README instructions for:
# 1. Wallet infrastructure setup
# 2. Authentication configuration
# 3. Token contract integration

# Copy example configs for core components
for component in server runner client; do
    cp parity-${component}/.env.sample \
       parity-${component}/.env
done
```
##### Deployed Addresses

For reference, here are the current deployed addresses on Ethereum:

- **Stake Wallet Address**: `0x261259e9467E042DBBF372906e17b94fC06942f2`
- **Token Contract Address**: `0x844303bcC1a347bE6B409Ae159b4040d84876024`



#### 3. Component Configuration

Each component requires specific configuration. Follow these steps carefully:

##### Server Setup

1. Navigate to `parity-server` directory
2. Open `.env`
3. Configure according to the [server documentation](https://github.com/theblitlabs/parity-server)
4. Key focus areas:
   - Database configuration
   - Blockchain connection parameters
   - Network settings

##### Runner Setup

1. Navigate to `parity-runner` directory
2. Open `.env`
3. Configure according to the [runner documentation](https://github.com/theblitlabs/parity-runner)
4. Key focus areas:
   - Docker environment settings
   - Server connection parameters
   - Resource allocation limits

##### Client Setup

1. Navigate to `parity-client` directory
2. Open `.env`
3. Configure according to the [client documentation](https://github.com/theblitlabs/parity-client)
4. Key focus areas:
   - API configurations
   - Server connection details
   - Client-specific parameters

⚠️ **Important Configuration Notes**:

- Verify all placeholder values are replaced with actual configurations
- Ensure no port conflicts between components
- Double-check file permissions
- Use secure values for authentication tokens and keys

#### 4. Launch Services

Run each component in a separate terminal window:

```bash
# Terminal 1: Server
cd parity-server
make build
parity-server auth --private-key YOUR_PRIVATE_KEY
parity-server

# Terminal 2: Runner
cd parity-runner
make build
parity-runner auth --private-key YOUR_PRIVATE_KEY
parity-runner stake --amount 10
parity-runner

# Terminal 3: Client
cd parity-client
make build
parity-client
```

## Contributing

We welcome contributions from the community! Here's how to get started:

1. **Find an Issue**
   Browse our repositories for open issues:

   - [parity-server](https://github.com/theblitlabs/parity-server/issues)
   - [parity-runner](https://github.com/theblitlabs/parity-runner/issues)
   - [parity-client](https://github.com/theblitlabs/parity-client/issues)
   - [parity-token](https://github.com/theblitlabs/parity-token/issues)
   - [parity-wallet](https://github.com/theblitlabs/parity-wallet/issues)

2. **Fork & Clone**
   Fork the relevant repository and create your feature branch:

   ```bash
   git checkout -b feature/amazing-feature
   ```

3. **Set Up Development Environment**

   ```bash
   make install-hooks
   ```

4. **Submit Your Work**
   - Follow [Conventional Commits](https://www.conventionalcommits.org/) guidelines
   - Include comprehensive documentation updates
   - Provide clear descriptions of changes
   - Reference the issue being addressed

---

📝 For detailed information about each component, please refer to their respective documentation.
