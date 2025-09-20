---
title: Quick Start Guide
excerpt: >-
  Get OllamaFlow up and running in minutes. This guide covers the fastest path
  to a working OllamaFlow deployment.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Prerequisites

Before starting, ensure you have:

* One or more Ollama instances running
* .NET 8.0 Runtime (for source deployment) or Docker (for container deployment)
* Network connectivity between OllamaFlow and your Ollama instances

## Option 1: Docker Deployment (Recommended)

The fastest way to get started is using the official Docker image.

### Pull and Run

```bash
# Pull the latest image
docker pull jchristn/ollamaflow

# Run with default configuration and persistent data
docker run -d \
  --name ollamaflow \
  -p 43411:43411 \
  -v $(pwd)/ollamaflow-data:/app/data \
  jchristn/ollamaflow
```

### Custom Configuration

Create a configuration file to customize OllamaFlow behavior:

```bash
# Create data directory
mkdir -p ollamaflow-data

# Create configuration file
cat > ollamaflow.json << 'EOF'
{
  "Webserver": {
    "Hostname": "*",
    "Port": 43411
  },
  "Logging": {
    "MinimumSeverity": "Info",
    "ConsoleLogging": true
  },
  "DatabaseFilename": "/app/data/ollamaflow.db",
  "AdminBearerTokens": [
    "your-secure-admin-token"
  ]
}
EOF

# Run with custom configuration and persistent database
docker run -d \
  --name ollamaflow \
  -p 43411:43411 \
  -v $(pwd)/ollamaflow.json:/app/ollamaflow.json:ro \
  -v $(pwd)/ollamaflow-data:/app/data \
  jchristn/ollamaflow
```

## Option 2: Build from Source

For development or customization, build and run from source.

### Clone and Build

```bash
# Clone the repository
git clone https://github.com/jchristn/ollamaflow.git
cd ollamaflow

# Build the solution
dotnet build src/OllamaFlow.sln

# Navigate to server directory
cd src/OllamaFlow.Server

# Run the server
dotnet run
```

### Run from Binaries

```bash
# Build and run from compiled binaries
dotnet build -c Release
cd bin/Release/net8.0
dotnet OllamaFlow.Server.dll
```

## Initial Configuration

When OllamaFlow starts for the first time, it creates:

1. **Default Configuration** (`ollamaflow.json`)
   * Webserver on port 43411
   * Default admin token: `ollamaflowadmin`
   * Console logging enabled

2. **SQLite Database** (`ollamaflow.db`)
   * Default frontend: `frontend1`
   * Default backend: `backend1` (localhost:11434)

## Verify Installation

Test that OllamaFlow is running correctly:

```bash
# Check connectivity
curl -I http://localhost:43411/

# Expected response
HTTP/1.1 200 OK
Content-Type: application/json
...
```

## Configure Your First Backend

Add your Ollama instance as a backend:

```bash
# Add a backend (replace with your Ollama server details)
curl -X PUT \
  -H "Authorization: Bearer ollamaflowadmin" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "my-ollama-server",
    "Name": "My Ollama Instance",
    "Hostname": "192.168.1.100",
    "Port": 11434,
    "MaxParallelRequests": 4
  }' \
  http://localhost:43411/v1.0/backends
```

## Configure Your Frontend

Update the frontend to use your backend:

```bash
# Update the default frontend
curl -X PUT \
  -H "Authorization: Bearer ollamaflowadmin" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "frontend1",
    "Name": "My AI Frontend",
    "Hostname": "*",
    "LoadBalancing": "RoundRobin",
    "Backends": ["my-ollama-server"],
    "RequiredModels": ["llama3"]
  }' \
  http://localhost:43411/v1.0/frontends/frontend1
```

## Test AI Inference

Now test that requests are properly routed:

```bash
# Generate text using the Ollama API through OllamaFlow
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3",
    "prompt": "Why is the sky blue?",
    "stream": false
  }' \
  http://localhost:43411/api/generate
```

## Monitor Your Deployment

Check the health of your backends:

```bash
# View all backends and their health status
curl -H "Authorization: Bearer ollamaflowadmin" \
  http://localhost:43411/v1.0/backends/health
```

## Multi-Node Testing Setup

To test with multiple Ollama instances, use the provided Docker Compose setup:

```bash
# Navigate to Docker directory in the OllamaFlow repository
cd Docker

# Start multiple Ollama instances for testing
docker compose -f compose-ollama.yaml up -d

# This creates 4 Ollama instances on ports 11435-11438
```

Then configure OllamaFlow to use these test instances:

```bash
# Add multiple backends
for i in {1..4}; do
  port=$((11434 + i))
  curl -X PUT \
    -H "Authorization: Bearer ollamaflowadmin" \
    -H "Content-Type: application/json" \
    -d "{
      \"Identifier\": \"test-backend-$i\",
      \"Name\": \"Test Backend $i\",
      \"Hostname\": \"localhost\",
      \"Port\": $port
    }" \
    http://localhost:43411/v1.0/backends
done

# Update frontend to use all test backends
curl -X PUT \
  -H "Authorization: Bearer ollamaflowadmin" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "frontend1",
    "Name": "Load Balanced Frontend",
    "Hostname": "*",
    "LoadBalancing": "RoundRobin",
    "Backends": ["test-backend-1", "test-backend-2", "test-backend-3", "test-backend-4"]
  }' \
  http://localhost:43411/v1.0/frontends/frontend1
```

## Common Issues

### Port Already in Use

If port 43411 is already in use, modify the configuration:

```json
{
  "Webserver": {
    "Port": 8080
  }
}
```

### Backend Connection Failed

Verify your Ollama instance is running and accessible:

```bash
# Test direct connection to Ollama
curl http://your-ollama-host:11434/api/version
```

### Authentication Failed

Ensure you're using the correct bearer token:

```bash
# Check your configuration file for the correct token
grep -A 5 "AdminBearerTokens" ollamaflow.json
```