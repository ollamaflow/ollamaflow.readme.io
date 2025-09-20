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
# Introduction to OllamaFlow

OllamaFlow is an intelligent load balancer and model orchestration platform designed to transform multiple Ollama instances into a unified, high-availability AI inference cluster. Whether you're scaling AI workloads across multiple GPUs, ensuring zero-downtime model serving, or managing a distributed AI infrastructure, OllamaFlow provides the orchestration layer you need.

OllamaFlow can be managed via the REST API documented here or using the [web dashboard](https://github.com/ollamaflow/ui) .

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

## Next Steps

* Learn about [Core Concepts](core-concepts.md) to understand frontends, backends, and models
* Follow the [Quick Start Guide](quick-start.md) to get OllamaFlow running
* Explore [Deployment Options](deployment-options.md) for your infrastructure
* Review the [API Reference](api-reference.md) for integration details
