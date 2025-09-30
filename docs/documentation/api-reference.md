---
title: API Reference
excerpt: >-
  OllamaFlow provides three sets of APIs: **Ollama-compatible APIs** and
  **OpenAI-compatible APIs** for AI inference, plus **Administrative APIs** for
  cluster management. All APIs support JSON request/response format and maintain
  full compatibility with existing Ollama and OpenAI clients.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Base URL and Authentication

* **Base URL**: `http://your-ollamaflow-host:43411`
* **Admin Authentication**: Bearer token required for administrative endpoints
* **Ollama APIs**: No authentication required (proxied to backends)
* **OpenAI APIs**: No authentication required (proxied to backends)

### Authentication Header

```bash
# For administrative APIs
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/backends
```

## API Compatibility

OllamaFlow supports both Ollama and OpenAI-compatible API formats, allowing clients to use either API style without modification.

## Ollama-Compatible APIs

These endpoints maintain full compatibility with the Ollama API, allowing existing clients to work without modification.

### Generate Completion

Generate text completions using a specified model.

**POST** `/api/generate`

#### Request Body

```json
{
  "model": "llama3:8b",
  "prompt": "Why is the sky blue?",
  "stream": true,
  "options": {
    "temperature": 0.8,
    "num_predict": 100,
    "top_k": 20,
    "top_p": 0.9
  }
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3:8b",
    "prompt": "Explain quantum computing in simple terms",
    "stream": false,
    "options": {
      "temperature": 0.7,
      "num_predict": 200
    }
  }' \
  http://localhost:43411/api/generate
```

#### Response

```json
{
  "model": "llama3:8b",
  "created_at": "2024-01-15T10:30:00.123456Z",
  "response": "Quantum computing is a revolutionary technology...",
  "done": true,
  "context": [1, 2, 3, 4, 5],
  "total_duration": 1234567890,
  "load_duration": 123456789,
  "prompt_eval_count": 10,
  "prompt_eval_duration": 234567890,
  "eval_count": 25,
  "eval_duration": 876543210
}
```

### Chat Completion

Generate chat-style completions with conversation context.

**POST** `/api/chat`

#### Request Body

```json
{
  "model": "llama3:8b",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful AI assistant."
    },
    {
      "role": "user",
      "content": "What is machine learning?"
    }
  ],
  "stream": true,
  "options": {
    "temperature": 0.8,
    "num_ctx": 2048
  }
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3:8b",
    "stream": false,
    "messages": [
      {
        "role": "system",
        "content": "You are a helpful assistant specializing in technology."
      },
      {
        "role": "user",
        "content": "Explain the difference between AI and ML"
      }
    ]
  }' \
  http://localhost:43411/api/chat
```

### Pull Model

Download a model to the backend instances.

**POST** `/api/pull`

#### Request Body

```json
{
  "model": "llama3:8b",
  "insecure": false,
  "stream": true
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "mistral:7b"
  }' \
  http://localhost:43411/api/pull
```

### Show Model Information

Get detailed information about a specific model.

**POST** `/api/show`

#### Request Body

```json
{
  "name": "llama3:8b",
  "verbose": true
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "name": "llama3:8b"
  }' \
  http://localhost:43411/api/show
```

### List Models

Get a list of available models across all backends.

**GET** `/api/tags`

#### cURL Example

```bash
curl http://localhost:43411/api/tags
```

#### Response

```json
{
  "models": [
    {
      "name": "llama3:8b",
      "model": "llama3:8b",
      "modified_at": "2024-01-15T10:30:00.123456Z",
      "size": 4661224576,
      "digest": "sha256:8934d96d3f08...",
      "details": {
        "parent_model": "",
        "format": "gguf",
        "family": "llama",
        "families": ["llama"],
        "parameter_size": "8B",
        "quantization_level": "Q4_0"
      }
    }
  ]
}
```

### List Running Models

Get information about currently running models.

**GET** `/api/ps`

#### cURL Example

```bash
curl http://localhost:43411/api/ps
```

### Generate Embeddings

Generate embeddings for text input.

**POST** `/api/embed`

#### Request Body

```json
{
  "model": "nomic-embed-text",
  "input": "The quick brown fox jumps over the lazy dog"
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "nomic-embed-text",
    "input": ["Hello world", "How are you?"]
  }' \
  http://localhost:43411/api/embed
```

### Delete Model

Remove a model from backend instances.

**DELETE** `/api/delete`

#### Request Body

```json
{
  "name": "llama3:8b"
}
```

#### cURL Example

```bash
curl -X DELETE \
  -H "Content-Type: application/json" \
  -d '{
    "name": "old-model:7b"
  }' \
  http://localhost:43411/api/delete
```

## OpenAI-Compatible APIs

OllamaFlow also supports OpenAI-compatible API endpoints, allowing existing OpenAI clients and tools to work seamlessly.

### Generate Completion

Generate text completions using OpenAI-compatible format.

**POST** `/v1/completions`

#### Request Body

```json
{
  "model": "llama3:8b",
  "prompt": "Why is the sky blue?",
  "max_tokens": 100,
  "temperature": 0.8,
  "top_p": 0.9,
  "stream": false
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3:8b",
    "prompt": "Explain quantum computing in simple terms",
    "max_tokens": 200,
    "temperature": 0.7,
    "stream": false
  }' \
  http://localhost:43411/v1/completions
```

### Chat Completion

Generate chat-style completions using OpenAI-compatible format.

**POST** `/v1/chat/completions`

#### Request Body

```json
{
  "model": "llama3:8b",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful AI assistant."
    },
    {
      "role": "user",
      "content": "What is machine learning?"
    }
  ],
  "max_tokens": 150,
  "temperature": 0.8,
  "stream": false
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3:8b",
    "messages": [
      {
        "role": "system",
        "content": "You are a helpful assistant specializing in technology."
      },
      {
        "role": "user",
        "content": "Explain the difference between AI and ML"
      }
    ],
    "max_tokens": 150,
    "temperature": 0.7
  }' \
  http://localhost:43411/v1/chat/completions
```

### Generate Embeddings

Generate embeddings using OpenAI-compatible format.

**POST** `/v1/embeddings`

#### Request Body

```json
{
  "model": "nomic-embed-text",
  "input": "The quick brown fox jumps over the lazy dog"
}
```

#### cURL Example

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "nomic-embed-text",
    "input": ["Hello world", "How are you?"]
  }' \
  http://localhost:43411/v1/embeddings
```

#### Response

```json
{
  "object": "list",
  "data": [
    {
      "object": "embedding",
      "embedding": [0.1, 0.2, 0.3, ...],
      "index": 0
    }
  ],
  "model": "nomic-embed-text",
  "usage": {
    "prompt_tokens": 8,
    "total_tokens": 8
  }
}
```

### List Models

Get available models using OpenAI-compatible format.

**GET** `/v1/models`

#### cURL Example

```bash
curl http://localhost:43411/v1/models
```

#### Response

```json
{
  "object": "list",
  "data": [
    {
      "id": "llama3:8b",
      "object": "model",
      "created": 1704067200,
      "owned_by": "ollama"
    }
  ]
}
```

## Administrative APIs

These endpoints provide cluster management capabilities and require bearer token authentication.

### Frontend Management

#### List All Frontends

**GET** `/v1.0/frontends`

```bash
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/frontends
```

#### Get Frontend

**GET** `/v1.0/frontends/{identifier}`

```bash
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/frontends/frontend1
```

#### Create Frontend

**PUT** `/v1.0/frontends`

```bash
curl -X PUT \
  -H "Authorization: Bearer your-admin-token" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "production-frontend",
    "Name": "Production AI Inference",
    "Hostname": "ai.company.com",
    "LoadBalancing": "RoundRobin",
    "TimeoutMs": 90000,
    "Backends": ["gpu-1", "gpu-2", "gpu-3"],
    "RequiredModels": ["llama3:8b", "mistral:7b"],
    "MaxRequestBodySize": 1073741824,
    "UseStickySessions": true,
    "StickySessionExpirationMs": 3600000
  }' \
  http://localhost:43411/v1.0/frontends
```

#### Update Frontend

**PUT** `/v1.0/frontends/{identifier}`

```bash
curl -X PUT \
  -H "Authorization: Bearer your-admin-token" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "production-frontend",
    "Name": "Updated Production Frontend",
    "Hostname": "*",
    "LoadBalancing": "Random",
    "Backends": ["gpu-1", "gpu-2", "gpu-3", "gpu-4"],
    "RequiredModels": ["llama3:8b", "mistral:7b", "codellama:13b"]
  }' \
  http://localhost:43411/v1.0/frontends/production-frontend
```

#### Delete Frontend

**DELETE** `/v1.0/frontends/{identifier}`

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/frontends/old-frontend
```

### Backend Management

#### List All Backends

**GET** `/v1.0/backends`

```bash
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/backends
```

#### Get Backend

**GET** `/v1.0/backends/{identifier}`

```bash
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/backends/gpu-1
```

#### Create Backend

**PUT** `/v1.0/backends`

```bash
curl -X PUT \
  -H "Authorization: Bearer your-admin-token" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "gpu-server-4",
    "Name": "GPU Server 4",
    "Hostname": "192.168.1.104",
    "Port": 11434,
    "Ssl": false,
    "HealthCheckUrl": "/api/version",
    "HealthCheckMethod": "GET",
    "UnhealthyThreshold": 3,
    "HealthyThreshold": 2,
    "MaxParallelRequests": 8,
    "RateLimitRequestsThreshold": 20,
    "LogRequestBody": false,
    "LogResponseBody": false
  }' \
  http://localhost:43411/v1.0/backends
```

#### Update Backend

**PUT** `/v1.0/backends/{identifier}`

```bash
curl -X PUT \
  -H "Authorization: Bearer your-admin-token" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "gpu-server-1",
    "Name": "Updated GPU Server 1",
    "Hostname": "192.168.1.101",
    "Port": 11434,
    "MaxParallelRequests": 12,
    "UnhealthyThreshold": 2
  }' \
  http://localhost:43411/v1.0/backends/gpu-server-1
```

#### Delete Backend

**DELETE** `/v1.0/backends/{identifier}`

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/backends/old-backend
```

### Health Monitoring

#### Get All Backend Health

**GET** `/v1.0/backends/health`

```bash
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/backends/health
```

#### Response

```json
[
  {
    "Identifier": "backend1",
    "Name": "My localhost Ollama instance",
    "Hostname": "localhost",
    "Port": 11434,
    "Ssl": false,
    "UnhealthyThreshold": 2,
    "HealthyThreshold": 2,
    "HealthCheckMethod": {
      "Method": "GET"
    },
    "HealthCheckUrl": "/",
    "MaxParallelRequests": 4,
    "RateLimitRequestsThreshold": 10,
    "LogRequestFull": false,
    "LogRequestBody": false,
    "LogResponseBody": false,
    "ApiFormat": "Ollama",
    "PinnedEmbeddingsProperties": {},
    "PinnedCompletionsProperties": {
      "model": "qwen2.5:3b",
      "options": {
        "temperature": 0.1,
        "howdy": "doody"
      }
    },
    "AllowEmbeddings": true,
    "AllowCompletions": true,
    "Active": true,
    "CreatedUtc": "2025-09-29T23:15:45.659639Z",
    "LastUpdateUtc": "2025-09-29T23:19:07.346900Z",
    "HealthySinceUtc": "2025-09-30T01:53:21.026058Z",
    "Uptime": "00:25:52.4859452",
    "ActiveRequests": 0,
    "IsSticky": false
  }
]
```

#### Get Single Backend Health

**GET** `/v1.0/backends/{identifier}/health`

```bash
curl -H "Authorization: Bearer your-admin-token" \
  http://localhost:43411/v1.0/backends/backend1/health
```

#### Response

```json
{
  "Identifier": "backend1",
  "Name": "My localhost Ollama instance",
  "Hostname": "localhost",
  "Port": 11434,
  "Ssl": false,
  "UnhealthyThreshold": 2,
  "HealthyThreshold": 2,
  "HealthCheckMethod": {
    "Method": "GET"
  },
  "HealthCheckUrl": "/",
  "MaxParallelRequests": 4,
  "RateLimitRequestsThreshold": 10,
  "LogRequestFull": false,
  "LogRequestBody": false,
  "LogResponseBody": false,
  "ApiFormat": "Ollama",
  "PinnedEmbeddingsProperties": {},
  "PinnedCompletionsProperties": {
    "model": "qwen2.5:3b",
    "options": {
      "temperature": 0.1,
      "howdy": "doody"
    }
  },
  "AllowEmbeddings": true,
  "AllowCompletions": true,
  "Active": true,
  "CreatedUtc": "2025-09-29T23:15:45.659639Z",
  "LastUpdateUtc": "2025-09-29T23:19:07.346900Z",
  "HealthySinceUtc": "2025-09-30T01:53:21.026058Z",
  "Uptime": "00:26:32.4690556",
  "ActiveRequests": 0,
  "IsSticky": false
}
```

## Error Responses

All APIs return standard HTTP status codes and JSON error responses.

### Error Response Format

```json
{
  "error": "BadRequest",
  "message": "Invalid request format",
  "details": "Missing required field: model",
  "timestamp": "2024-01-15T10:30:00.123456Z",
  "requestId": "12345678-1234-1234-1234-123456789012"
}
```

### Common Error Codes

| Status | Error Type         | Description                          |
| ------ | ------------------ | ------------------------------------ |
| 400    | BadRequest         | Invalid request format or parameters |
| 401    | Unauthorized       | Missing or invalid bearer token      |
| 404    | NotFound           | Resource not found                   |
| 409    | Conflict           | Resource already exists or conflict  |
| 429    | TooManyRequests    | Rate limit exceeded                  |
| 500    | InternalError      | Server error                         |
| 502    | BadGateway         | Backend unavailable                  |
| 503    | ServiceUnavailable | No healthy backends available        |

## Rate Limiting

OllamaFlow implements rate limiting at the backend level:

* Each backend has a configurable `RateLimitRequestsThreshold`
* Requests exceeding the threshold receive `429 Too Many Requests`
* Rate limiting is applied per backend, not globally

## Streaming Responses

Both Ollama APIs and admin APIs support streaming where applicable:

* **Text Generation**: Set `"stream": true` for real-time token streaming
* **Model Downloads**: Progress updates during model pulls
* **Health Monitoring**: Server-sent events for real-time status updates

### Streaming Example

```bash
# Stream text generation
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3:8b",
    "prompt": "Write a story about space exploration",
    "stream": true
  }' \
  http://localhost:43411/api/generate
```

## Request Headers

### Standard Headers

* `Content-Type: application/json` - Required for POST/PUT requests
* `Accept: application/json` - Recommended for consistent responses
* `User-Agent: your-client/1.0` - Optional client identification

### Custom Headers

* `X-Request-ID: uuid` - Optional request tracking
* `X-Frontend-Hint: frontend-id` - Optional frontend selection hint

### Response Headers

* `X-Request-ID: uuid` - Request tracking identifier
* `X-Backend-Used: backend-id` - Which backend processed the request
* `X-Model-Synchronized: true/false` - Whether model sync was required

## Postman Collection

A complete Postman collection with all API endpoints and examples is available in the OllamaFlow repository:

**Download**: [OllamaFlow.postman\_collection.json](https://github.com/jchristn/ollamaflow/blob/main/OllamaFlow.postman_collection.json)

The collection includes:

* All Ollama-compatible endpoints with sample requests
* Complete admin API coverage with authentication
* Environment variables for easy configuration
* Response examples and test scripts

## Security and Access Control

OllamaFlow provides comprehensive security controls through Frontend and Backend configuration:

### Request Type Controls

* **AllowEmbeddings**: Controls access to embeddings endpoints
  - Ollama API: `/api/embed`
  - OpenAI API: `/v1/embeddings`
* **AllowCompletions**: Controls access to completion endpoints
  - Ollama API: `/api/generate`, `/api/chat`
  - OpenAI API: `/v1/completions`, `/v1/chat/completions`

For a request to succeed, both the frontend and at least one assigned backend must allow the request type.

### Pinned Properties

Administrators can enforce specific parameters in requests through pinned properties:

* **PinnedEmbeddingsProperties**: Key-value pairs merged into all embeddings requests
* **PinnedCompletionsProperties**: Key-value pairs merged into all completion requests

Pinned properties take precedence over client-specified values, enabling organizational compliance and standardization.

### Example Security Configuration

```bash
# Create a frontend that only allows completions with enforced temperature
curl -X PUT \
  -H "Authorization: Bearer your-admin-token" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "secure-frontend",
    "Name": "Secure Completions Only",
    "AllowEmbeddings": false,
    "AllowCompletions": true,
    "PinnedCompletionsProperties": {
      "options": {
        "temperature": 0.7,
        "num_ctx": 2048
      }
    },
    "Backends": ["secure-backend"]
  }' \
  http://localhost:43411/v1.0/frontends
```

With this configuration:
- ✅ **Allowed**: Completion requests to both API formats
  - `POST /api/generate` (Ollama)
  - `POST /api/chat` (Ollama)
  - `POST /v1/completions` (OpenAI)
  - `POST /v1/chat/completions` (OpenAI)
- ❌ **Blocked**: Embeddings requests to both API formats
  - `POST /api/embed` (Ollama)
  - `POST /v1/embeddings` (OpenAI)

## API Explorer

OllamaFlow includes a companion web-based API Explorer for testing and validation:

* **Repository**: [https://github.com/ollamaflow/apiexplorer](https://github.com/ollamaflow/apiexplorer)
* **Purpose**: Test and evaluate APIs in scaled inference architectures
* **Features**: Real-time API testing, JSON validation, response inspection
* **Formats**: Supports both Ollama and OpenAI API formats

The API Explorer provides an intuitive interface for development debugging, load testing, and integration validation.

## SDK and Client Libraries

OllamaFlow supports both Ollama and OpenAI client libraries:

### Ollama-Compatible Libraries

* **Python**: `ollama-python`
* **JavaScript**: `ollama-js`
* **Go**: `ollama-go`
* **Rust**: `ollama-rs`
* **Java**: `ollama-java`

### OpenAI-Compatible Libraries

* **Python**: `openai` (official OpenAI Python library)
* **JavaScript**: `openai` (official OpenAI Node.js library)
* **Go**: `go-openai`
* **Rust**: `async-openai`
* **Java**: `openai-java`

Simply point these libraries to your OllamaFlow endpoint instead of a direct Ollama or OpenAI instance. For OpenAI libraries, use the base URL `http://your-ollamaflow-host:43411/v1`.

## Next Steps

* Explore [Configuration Examples](configuration-examples.md) for common scenarios
* Review [REST API Basics](rest-api-basics.md) for API fundamentals
* Check [Monitoring and Observability](monitoring.md) for production insights
