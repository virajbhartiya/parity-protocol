# Parity Protocol LLM Models Guide

## Overview

Parity Protocol provides a comprehensive LLM (Large Language Model) inference system that enables distributed AI processing across the network. Runners can host various LLM models and earn USDFC tokens for providing inference services to clients.

## 🧠 Supported LLM Models

### Model Categories

Parity Protocol supports multiple categories of language models:

1. **Chat Models**: Conversational AI (Llama, Mistral, CodeLlama)
2. **Completion Models**: Text completion and generation
3. **Code Models**: Programming assistance and code generation
4. **Embedding Models**: Text embeddings and similarity
5. **Custom Models**: User-provided models

### Popular Models

| Model Name    | Size   | Use Case           | Memory Required |
| ------------- | ------ | ------------------ | --------------- |
| Llama 3.1 8B  | 8B     | General chat       | 8-16 GB         |
| Llama 3.1 70B | 70B    | Advanced reasoning | 40-80 GB        |
| CodeLlama 7B  | 7B     | Code generation    | 8-14 GB         |
| Mistral 7B    | 7B     | Efficient chat     | 6-12 GB         |
| Qwen 2.5      | 7B-72B | Multilingual       | 6-40 GB         |
| Gemma 2       | 2B-27B | Google models      | 4-20 GB         |

## 🚀 Quick Start

### 1. Install Ollama (Recommended)

```bash
# Install Ollama for model management
curl -fsSL https://ollama.ai/install.sh | sh

# Start Ollama service
ollama serve
```

### 2. Download Models

```bash
# Download popular models
ollama pull llama3.1:8b
ollama pull codellama:7b
ollama pull mistral:7b

# List available models
ollama list
```

### 3. Configure Runner

Update your runner configuration:

```bash
# In parity-runner/.env
OLLAMA_ENABLED=true
OLLAMA_HOST=127.0.0.1:11434
OLLAMA_MODELS="llama3.1:8b,codellama:7b,mistral:7b"
LLM_INFERENCE_ENABLED=true
LLM_MAX_TOKENS=4096
LLM_CONCURRENT_REQUESTS=3
```

### 4. Start Runner

```bash
./parity-runner start
```

The runner will automatically:

- Detect available models
- Register model capabilities with the server
- Start accepting inference requests

## 🔧 Model Management

### Loading Models

```bash
# Load specific models
ollama pull llama3.1:8b-instruct-q4_0
ollama pull codellama:13b-code

# Load quantized models for efficiency
ollama pull llama3.1:8b-instruct-q8_0  # 8-bit quantization
ollama pull llama3.1:8b-instruct-q4_0  # 4-bit quantization
```

### Model Variants

Different quantization levels offer trade-offs:

- **q8_0**: Highest quality, more memory
- **q6_K**: Good quality, moderate memory
- **q4_0**: Balanced quality/memory
- **q3_K_S**: Lower memory, acceptable quality
- **q2_K**: Minimal memory, basic quality

### Custom Models

Load your own models:

```bash
# Create a Modelfile
echo 'FROM /path/to/your/model.gguf' > Modelfile
echo 'PARAMETER temperature 0.7' >> Modelfile
echo 'SYSTEM "You are a helpful assistant."' >> Modelfile

# Create the model
ollama create mymodel -f Modelfile

# Test the model
ollama run mymodel "Hello, how are you?"
```

## 💬 Inference Usage

### Basic Chat

```bash
# Simple chat completion
curl -X POST http://localhost:8080/api/v1/llm/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1:8b",
    "messages": [
      {"role": "user", "content": "Explain quantum computing"}
    ],
    "max_tokens": 500,
    "temperature": 0.7
  }'
```

### Code Generation

```bash
# Code generation request
curl -X POST http://localhost:8080/api/v1/llm/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "codellama:7b",
    "messages": [
      {"role": "user", "content": "Write a Python function to calculate Fibonacci numbers"}
    ],
    "max_tokens": 300,
    "temperature": 0.2
  }'
```

### Streaming Responses

```bash
# Stream responses for real-time output
curl -X POST http://localhost:8080/api/v1/llm/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1:8b",
    "messages": [
      {"role": "user", "content": "Write a short story about AI"}
    ],
    "stream": true,
    "max_tokens": 800
  }'
```

## 💰 Economics and Rewards

### Pricing Model

Rewards are calculated based on:

```
Reward = Base_Rate × Tokens_Generated × Model_Multiplier × Quality_Bonus
```

**Factors**:

- **Base Rate**: 0.001 USDFC per token (configurable)
- **Model Multiplier**: Larger models get higher rates
- **Quality Bonus**: Based on response quality metrics
- **Speed Bonus**: Faster inference gets small bonus

### Model Multipliers

| Model Size | Multiplier | Example Reward per 1000 tokens |
| ---------- | ---------- | ------------------------------ |
| < 3B       | 1.0x       | 1.0 USDFC                      |
| 3B - 8B    | 1.5x       | 1.5 USDFC                      |
| 8B - 20B   | 2.0x       | 2.0 USDFC                      |
| 20B - 70B  | 3.0x       | 3.0 USDFC                      |
| > 70B      | 4.0x       | 4.0 USDFC                      |

## 🔍 Monitoring and Analytics

### Runner Dashboard

Monitor your LLM performance:

```bash
# Check model status
./parity-runner status

# View earnings
./parity-runner balance

# Model performance metrics
./parity-runner llm-stats
```

Example output:

```
LLM Models Status:
├── llama3.1:8b (LOADED) - 156 requests today - 2.3 USDFC earned
├── codellama:7b (LOADED) - 89 requests today - 1.7 USDFC earned
└── mistral:7b (STANDBY) - 12 requests today - 0.4 USDFC earned

Total Today: 257 requests, 4.4 USDFC earned
Average Response Time: 2.3 seconds
Success Rate: 98.4%
```

## �� Troubleshooting

### Common Issues

1. **Model Won't Load**

   ```
   Error: insufficient memory
   Solution: Use smaller/quantized models or increase system RAM
   ```

2. **Slow Inference**

   ```
   Problem: High response times
   Solutions:
   - Enable GPU acceleration
   - Use quantized models
   - Reduce max_tokens
   - Increase GPU layers
   ```

3. **GPU Memory Issues**
   ```
   Problem: CUDA out of memory
   Solutions:
   - Reduce OLLAMA_GPU_LAYERS
   - Use smaller models
   - Limit concurrent requests
   ```

## 🎯 Best Practices

1. **Model Selection**

   - Choose models appropriate for your hardware
   - Use quantized models for better performance
   - Consider context length requirements
   - Balance quality vs. speed needs

2. **Resource Management**

   - Monitor memory and GPU usage
   - Implement model swapping for efficiency
   - Set appropriate concurrency limits
   - Use model-specific optimizations

3. **Economic Optimization**
   - Run popular models for higher demand
   - Optimize for fast inference speeds
   - Maintain high uptime and reliability
   - Monitor market pricing trends

This comprehensive LLM system provides a robust platform for distributed AI inference while ensuring fair compensation for compute providers and high-quality service for clients.
