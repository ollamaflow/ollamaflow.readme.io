---
title: Introduction
excerpt: >-
  This page will help you get started with OllamaFlow. You'll be up and running
  in a jiffy!
deprecated: false
hidden: false
metadata:
  robots: index
---
<br />

# Introduction to OllamaFlow

OllamaFlow is an intelligent load balancer and model orchestration platform designed to transform multiple Ollama instances into a unified, high-availability AI inference cluster. Whether you're scaling AI workloads across multiple GPUs, ensuring zero-downtime model serving, or managing a distributed AI infrastructure, OllamaFlow provides the orchestration layer you need.

## What is OllamaFlow?

OllamaFlow acts as an intelligent proxy layer that sits between your clients and multiple Ollama instances. It provides:

* **Smart Load Balancing**: Distributes requests across healthy backends using configurable algorithms
* **Automatic Model Synchronization**: Ensures required models are available across all backends
* **High Availability**: Real-time health monitoring with automatic failover
* **Virtual Endpoints**: Create multiple frontend endpoints, each with their own backend configurations
* **RESTful Management**: Full administrative control through comprehensive APIs

## Core Architecture

OllamaFlow consists of three main components:

### 1. **Frontends**

Virtual Ollama endpoints that clients connect to. Each frontend:

* Maps to a specific hostname or acts as a catch-all (`*`)
* Defines which backend Ollama instances to use
* Specifies required models for automatic synchronization
* Configures load balancing behavior and request handling

### 2. **Backends**

Physical Ollama instances in your infrastructure. Each backend:

* Represents an actual Ollama server (hostname:port)
* Has configurable health check parameters
* Supports request rate limiting and parallel request management
* Maintains model discovery and availability tracking

### 3. **Models**

AI models that are automatically managed across your fleet:

* **Model Discovery**: Automatic detection of available models on each backend
* **Model Synchronization**: Intelligent pulling of required models to ensure availability
* **Model Requirements**: Frontend-specific model requirements for automatic provisioning

## Key Benefits

### **Simplified Scaling**

Transform a single Ollama instance into a distributed cluster without changing client code. OllamaFlow maintains full API compatibility with Ollama.

### **Zero-Downtime Operations**

Automatic health monitoring and failover ensure your AI services remain available even when individual backends fail.

### **Intelligent Resource Management**

Smart load balancing and model synchronization optimize resource utilization across your infrastructure.

### **Enterprise-Ready**

Bearer token authentication, comprehensive logging, and RESTful administration APIs provide the management capabilities needed for production deployments.

## Use Cases

* **GPU Cluster Management**: Distribute workloads across multiple GPU servers
* **High Availability AI Services**: Ensure 24/7 availability with automatic failover
* **Development & Testing**: Easy switching between different model configurations
* **Multi-Tenant Scenarios**: Isolate workloads while sharing infrastructure
* **Cost Optimization**: Maximize hardware utilization across your AI infrastructure

## API Explorer

OllamaFlow includes a companion web-based API Explorer for testing and evaluating APIs. The API Explorer provides an intuitive interface for:

* **API Testing**: Test both Ollama and OpenAI-compatible API formats
* **Real-time Validation**: Validate API requests with JSON syntax checking
* **Development Debugging**: Inspect response bodies and headers for troubleshooting
* **Load Testing**: Evaluate API performance under different conditions
* **Integration Testing**: Validate OllamaFlow behavior in scaled inference architectures

The API Explorer is available at: [https://github.com/ollamaflow/apiexplorer](https://github.com/ollamaflow/apiexplorer)

### Why the API Explorer is Useful

The API Explorer serves as a comprehensive user interface for OllamaFlow, providing:

* **No Setup Required**: Simple web-based interface that runs in any browser
* **Multi-Format Testing**: Test both Ollama and OpenAI API formats in one tool
* **Real-Time Feedback**: Immediate validation of requests and responses
* **Development Workflow**: Essential for debugging API integrations and testing configurations
* **Load Testing**: Evaluate performance characteristics before production deployment
* **Educational Tool**: Learn API patterns and explore model capabilities interactively

### Quick Start with API Explorer

1. **Clone the repository**:
   ```bash
   git clone https://github.com/ollamaflow/apiexplorer.git
   cd apiexplorer
   ```

2. **Open the explorer**:
   ```bash
   # Simply open index.html in your browser
   open index.html  # macOS
   # or
   xdg-open index.html  # Linux
   ```

3. **Configure for OllamaFlow**:
   * Set the base URL to your OllamaFlow instance (e.g., `http://localhost:43411`)
   * Select your preferred API format (Ollama or OpenAI)
   * Choose your model and start testing

The API Explorer supports both streaming and non-streaming completions, embeddings testing, and provides detailed response inspection capabilities.

## Next Steps

* Learn about [Core Concepts](core-concepts.md) to understand frontends, backends, and models
* Follow the [Quick Start Guide](quick-start.md) to get OllamaFlow running
* Explore [Deployment Options](deployment-options.md) for your infrastructure
* Review the [API Reference](api-reference.md) for integration details
