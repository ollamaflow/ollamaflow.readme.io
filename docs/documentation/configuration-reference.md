---
title: Configuration Reference
excerpt: >-
  This comprehensive guide covers all configuration options available in
  OllamaFlow, including the main settings file (`ollamaflow.json`) and the
  structure of key objects like frontends and backends.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Main Configuration File

OllamaFlow uses a JSON configuration file (`ollamaflow.json`) that defines server settings, logging, and authentication. On first startup, a default configuration is created if none exists.

### Default Configuration Location

* **Docker**: `/app/ollamaflow.json`
* **Bare Metal**: Same directory as executable
* **Custom**: Specify with `OLLAMAFLOW_CONFIG` environment variable

### Complete Configuration Example

```json
{
  "Logging": {
    "Servers": [
      {
        "Hostname": "127.0.0.1",
        "Port": 514,
        "RandomizePorts": false,
        "MinimumPort": 65000,
        "MaximumPort": 65535
      }
    ],
    "LogDirectory": "./logs/",
    "LogFilename": "ollamaflow.log",
    "ConsoleLogging": true,
    "EnableColors": true,
    "MinimumSeverity": 1
  },
  "Webserver": {
    "Hostname": "*",
    "Port": 43411,
    "IO": {
      "StreamBufferSize": 65536,
      "MaxRequests": 1024,
      "ReadTimeoutMs": 10000,
      "MaxIncomingHeadersSize": 65536,
      "EnableKeepAlive": false
    },
    "Ssl": {
      "Enable": false,
      "MutuallyAuthenticate": false,
      "AcceptInvalidCertificates": true,
      "CertificateFile": "",
      "CertificatePassword": ""
    },
    "Headers": {
      "IncludeContentLength": true,
      "DefaultHeaders": {
        "Access-Control-Allow-Origin": "*",
        "Access-Control-Allow-Methods": "OPTIONS, HEAD, GET, PUT, POST, DELETE, PATCH",
        "Access-Control-Allow-Headers": "*",
        "Access-Control-Expose-Headers": "",
        "Accept": "*/*",
        "Accept-Language": "en-US, en",
        "Accept-Charset": "ISO-8859-1, utf-8",
        "Cache-Control": "no-cache",
        "Connection": "close",
        "Host": "localhost:43411"
      }
    },
    "AccessControl": {
      "DenyList": {},
      "PermitList": {},
      "Mode": "DefaultPermit"
    },
    "Debug": {
      "AccessControl": false,
      "Routing": false,
      "Requests": false,
      "Responses": false
    }
  },
  "DatabaseFilename": "ollamaflow.db",
  "AdminBearerTokens": [
    "your-secure-admin-token"
  ]
}
```

## Configuration Sections

### Logging Settings

Controls how OllamaFlow logs information and errors.

| Setting           | Type    | Default            | Description                                          |
| ----------------- | ------- | ------------------ | ---------------------------------------------------- |
| `LogDirectory`    | string  | `"./logs/"`        | Directory for log files                              |
| `LogFilename`     | string  | `"ollamaflow.log"` | Base filename for logs                               |
| `ConsoleLogging`  | boolean | `true`             | Enable console output                                |
| `EnableColors`    | boolean | `true`             | Enable colored console output                        |
| `MinimumSeverity` | integer | `1`                | Minimum log level (0=Debug, 1=Info, 2=Warn, 3=Error) |

#### Syslog Servers

Optional remote logging configuration:

```json
{
  "Servers": [
    {
      "Hostname": "syslog.company.com",
      "Port": 514,
      "RandomizePorts": false,
      "MinimumPort": 65000,
      "MaximumPort": 65535
    }
  ]
}
```

### Webserver Settings

Configures the HTTP server that handles all requests.

#### Basic Settings

| Setting    | Type    | Default | Description                          |
| ---------- | ------- | ------- | ------------------------------------ |
| `Hostname` | string  | `"*"`   | Bind hostname (* for all interfaces) |
| `Port`     | integer | `43411` | TCP port to listen on                |

#### IO Settings

Controls request handling and performance:

| Setting                  | Type    | Default | Description                          |
| ------------------------ | ------- | ------- | ------------------------------------ |
| `StreamBufferSize`       | integer | `65536` | Buffer size for streaming responses  |
| `MaxRequests`            | integer | `1024`  | Maximum concurrent requests          |
| `ReadTimeoutMs`          | integer | `10000` | Request read timeout in milliseconds |
| `MaxIncomingHeadersSize` | integer | `65536` | Maximum size of request headers      |
| `EnableKeepAlive`        | boolean | `false` | Enable HTTP keep-alive connections   |

#### SSL Settings

HTTPS configuration for secure connections:

| Setting                     | Type    | Default | Description                      |
| --------------------------- | ------- | ------- | -------------------------------- |
| `Enable`                    | boolean | `false` | Enable HTTPS                     |
| `MutuallyAuthenticate`      | boolean | `false` | Require client certificates      |
| `AcceptInvalidCertificates` | boolean | `true`  | Accept self-signed certificates  |
| `CertificateFile`           | string  | `""`    | Path to SSL certificate file     |
| `CertificatePassword`       | string  | `""`    | Certificate password if required |

#### Headers Settings

Default HTTP headers and CORS configuration:

```json
{
  "IncludeContentLength": true,
  "DefaultHeaders": {
    "Access-Control-Allow-Origin": "*",
    "Access-Control-Allow-Methods": "OPTIONS, HEAD, GET, PUT, POST, DELETE, PATCH",
    "Access-Control-Allow-Headers": "*"
  }
}
```

#### Access Control

IP-based access control (optional):

```json
{
  "DenyList": {
    "192.168.1.100": "Blocked IP",
    "10.0.0.0/8": "Blocked network"
  },
  "PermitList": {
    "192.168.1.0/24": "Allowed network"
  },
  "Mode": "DefaultPermit"
}
```

Modes:

* `DefaultPermit`: Allow all except denied IPs
* `DefaultDeny`: Deny all except permitted IPs

### Database Settings

| Setting            | Type   | Default           | Description               |
| ------------------ | ------ | ----------------- | ------------------------- |
| `DatabaseFilename` | string | `"ollamaflow.db"` | SQLite database file path |

### Authentication Settings

| Setting             | Type  | Default               | Description                        |
| ------------------- | ----- | --------------------- | ---------------------------------- |
| `AdminBearerTokens` | array | `["ollamaflowadmin"]` | Valid bearer tokens for admin APIs |

## Frontend Configuration

Frontends are virtual Ollama endpoints that clients connect to. They are stored in the database and managed via the API.

### Frontend Object Structure

```json
{
  "Identifier": "production-frontend",
  "Name": "Production AI Inference",
  "Hostname": "ai.company.com",
  "TimeoutMs": 90000,
  "LoadBalancing": "RoundRobin",
  "BlockHttp10": true,
  "MaxRequestBodySize": 1073741824,
  "Backends": ["gpu-1", "gpu-2", "gpu-3"],
  "RequiredModels": ["llama3:8b", "mistral:7b"],
  "LogRequestFull": false,
  "LogRequestBody": false,
  "LogResponseBody": false,
  "UseStickySessions": false,
  "StickySessionExpirationMs": 1800000,
  "Active": true
}
```

### Frontend Properties

| Property                    | Type    | Default        | Description                                      |
| --------------------------- | ------- | -------------- | ------------------------------------------------ |
| `Identifier`                | string  | Required       | Unique identifier for this frontend              |
| `Name`                      | string  | Required       | Human-readable name                              |
| `Hostname`                  | string  | `"*"`          | Hostname pattern (* for catch-all)               |
| `TimeoutMs`                 | integer | `60000`        | Request timeout in milliseconds                  |
| `LoadBalancing`             | enum    | `"RoundRobin"` | Load balancing algorithm                         |
| `BlockHttp10`               | boolean | `true`         | Reject HTTP/1.0 requests                         |
| `MaxRequestBodySize`        | integer | `536870912`    | Max request size in bytes (512MB)                |
| `Backends`                  | array   | `[]`           | List of backend identifiers                      |
| `RequiredModels`            | array   | `[]`           | Models that must be available                    |
| `UseStickySessions`         | boolean | `false`        | Enable session stickiness                        |
| `StickySessionExpirationMs` | integer | `1800000`      | Session timeout (30 minutes, min: 10s, max: 24h) |
| `LogRequestFull`            | boolean | `false`        | Log complete requests                            |
| `LogRequestBody`            | boolean | `false`        | Log request bodies                               |
| `LogResponseBody`           | boolean | `false`        | Log response bodies                              |
| `Active`                    | boolean | `true`         | Whether frontend is active                       |

### Load Balancing Options

* `"RoundRobin"`: Cycle through backends sequentially
* `"Random"`: Randomly select from healthy backends

### Hostname Patterns

* `"*"`: Match all hostnames (catch-all)
* `"api.company.com"`: Exact hostname match
* Multiple frontends can exist with different hostname patterns

## Backend Configuration

Backends represent physical Ollama instances in your infrastructure.

### Backend Object Structure

```json
{
  "Identifier": "gpu-server-1",
  "Name": "Primary GPU Server",
  "Hostname": "192.168.1.100",
  "Port": 11434,
  "Ssl": false,
  "UnhealthyThreshold": 3,
  "HealthyThreshold": 2,
  "HealthCheckMethod": "GET",
  "HealthCheckUrl": "/api/version",
  "MaxParallelRequests": 8,
  "RateLimitRequestsThreshold": 20,
  "LogRequestFull": false,
  "LogRequestBody": false,
  "LogResponseBody": false,
  "Active": true
}
```

### Backend Properties

| Property                     | Type    | Default  | Description                              |
| ---------------------------- | ------- | -------- | ---------------------------------------- |
| `Identifier`                 | string  | Required | Unique identifier for this backend       |
| `Name`                       | string  | Required | Human-readable name                      |
| `Hostname`                   | string  | Required | Backend server hostname/IP               |
| `Port`                       | integer | `11434`  | Backend server port                      |
| `Ssl`                        | boolean | `false`  | Use HTTPS for backend communication      |
| `UnhealthyThreshold`         | integer | `2`      | Failed checks before marking unhealthy   |
| `HealthyThreshold`           | integer | `2`      | Successful checks before marking healthy |
| `HealthCheckMethod`          | string  | `"GET"`  | HTTP method for health checks            |
| `HealthCheckUrl`             | string  | `"/"`    | URL path for health checks               |
| `MaxParallelRequests`        | integer | `4`      | Maximum concurrent requests              |
| `RateLimitRequestsThreshold` | integer | `10`     | Rate limiting threshold                  |
| `LogRequestFull`             | boolean | `false`  | Log complete requests                    |
| `LogRequestBody`             | boolean | `false`  | Log request bodies                       |
| `LogResponseBody`            | boolean | `false`  | Log response bodies                      |
| `Active`                     | boolean | `true`   | Whether backend is active                |

### Health Check Configuration

Health checks validate backend availability:

* **Method**: HTTP method (GET, HEAD, POST)
* **URL**: Path to check (e.g., `/`, `/api/version`, `/health`)
* **Thresholds**: Number of consecutive successes/failures to change state

Common health check endpoints:

* `/`: Basic connectivity check
* `/api/version`: Ollama version endpoint
* `/api/tags`: Model listing endpoint

### Rate Limiting

Backends can enforce rate limits:

* Requests exceeding `RateLimitRequestsThreshold` receive HTTP 429
* Rate limiting is per backend, not global
* Helps protect individual Ollama instances from overload

## Environment Variables

Override configuration with environment variables:

| Variable                 | Description                 | Example                       |
| ------------------------ | --------------------------- | ----------------------------- |
| `OLLAMAFLOW_CONFIG`      | Configuration file path     | `/etc/ollamaflow/config.json` |
| `OLLAMAFLOW_PORT`        | Override webserver port     | `8080`                        |
| `OLLAMAFLOW_HOSTNAME`    | Override webserver hostname | `0.0.0.0`                     |
| `OLLAMAFLOW_DATABASE`    | Override database file path | `/data/ollamaflow.db`         |
| `OLLAMAFLOW_ADMIN_TOKEN` | Override admin token        | `secure-production-token`     |
| `ASPNETCORE_ENVIRONMENT` | .NET environment            | `Production`                  |

### Docker Environment Example

```bash
docker run -d \
  -e OLLAMAFLOW_PORT=8080 \
  -e OLLAMAFLOW_ADMIN_TOKEN=my-secure-token \
  -e ASPNETCORE_ENVIRONMENT=Production \
  -p 8080:8080 \
  jchristn/ollamaflow
```

## Configuration Examples

### Basic Single Backend

Minimal configuration for testing:

```json
{
  "Webserver": {
    "Port": 43411
  },
  "AdminBearerTokens": ["test-token"]
}
```

Frontend/Backend via API:

```bash
# Create backend
curl -X PUT -H "Authorization: Bearer test-token" \
  -H "Content-Type: application/json" \
  -d '{"Identifier": "local", "Hostname": "localhost", "Port": 11434}' \
  http://localhost:43411/v1.0/backends

# Create frontend
curl -X PUT -H "Authorization: Bearer test-token" \
  -H "Content-Type: application/json" \
  -d '{"Identifier": "main", "Backends": ["local"]}' \
  http://localhost:43411/v1.0/frontends
```

### Production Multi-Backend

Production configuration with multiple GPU servers:

```json
{
  "Webserver": {
    "Hostname": "*",
    "Port": 43411,
    "Ssl": {
      "Enable": true,
      "CertificateFile": "/etc/ssl/ollamaflow.crt",
      "CertificatePassword": "cert-password"
    }
  },
  "Logging": {
    "LogDirectory": "/var/log/ollamaflow/",
    "ConsoleLogging": false,
    "MinimumSeverity": 1
  },
  "DatabaseFilename": "/var/lib/ollamaflow/ollamaflow.db",
  "AdminBearerTokens": [
    "secure-production-token-1",
    "secure-production-token-2"
  ]
}
```

Backends configuration:

```bash
# GPU servers
for i in {1..4}; do
  curl -X PUT -H "Authorization: Bearer secure-production-token-1" \
    -H "Content-Type: application/json" \
    -d "{
      \"Identifier\": \"gpu-$i\",
      \"Name\": \"GPU Server $i\",
      \"Hostname\": \"gpu$i.company.internal\",
      \"Port\": 11434,
      \"MaxParallelRequests\": 8,
      \"HealthCheckUrl\": \"/api/version\"
    }" \
    http://localhost:43411/v1.0/backends
done

# Production frontend
curl -X PUT -H "Authorization: Bearer secure-production-token-1" \
  -H "Content-Type: application/json" \
  -d '{
    "Identifier": "production",
    "Name": "Production AI Inference",
    "Hostname": "ai.company.com",
    "LoadBalancing": "RoundRobin",
    "Backends": ["gpu-1", "gpu-2", "gpu-3", "gpu-4"],
    "RequiredModels": ["llama3:8b", "mistral:7b", "codellama:13b"],
    "TimeoutMs": 120000
  }' \
  http://localhost:43411/v1.0/frontends
```

### Development Environment

Development setup with debugging enabled:

```json
{
  "Webserver": {
    "Port": 43411,
    "Debug": {
      "Routing": true,
      "Requests": true,
      "Responses": false
    }
  },
  "Logging": {
    "ConsoleLogging": true,
    "EnableColors": true,
    "MinimumSeverity": 0
  },
  "AdminBearerTokens": ["dev-token"]
}
```

## Configuration Validation

OllamaFlow validates configuration on startup:

### Common Validation Errors

1. **Invalid Port Range**: Ports must be 1-65535
2. **Missing Required Fields**: Identifier, Hostname required for backends
3. **Duplicate Identifiers**: Frontend/Backend IDs must be unique
4. **Invalid Load Balancing**: Must be "RoundRobin" or "Random"
5. **Invalid Hostnames**: Must be valid hostname or "*"

### Configuration Test

Validate configuration without starting the server:

```bash
# Test configuration file
dotnet OllamaFlow.Server.dll --validate-config

# Test with specific config
OLLAMAFLOW_CONFIG=/path/to/config.json dotnet OllamaFlow.Server.dll --validate-config
```

## Migration and Backup

### Database Backup

Backup the SQLite database regularly:

```bash
# Simple copy (stop OllamaFlow first)
cp ollamaflow.db ollamaflow.db.backup

# Online backup (while running)
sqlite3 ollamaflow.db ".backup /path/to/backup.db"
```

### Configuration Migration

When upgrading OllamaFlow:

1. **Backup Configuration**: Save current `ollamaflow.json`
2. **Backup Database**: Save current `ollamaflow.db`
3. **Review Changes**: Check for new configuration options
4. **Test Upgrade**: Test in non-production environment first

### Export/Import Configuration

Export current configuration for replication:

```bash
# Export all frontends
curl -H "Authorization: Bearer token" \
  http://localhost:43411/v1.0/frontends > frontends.json

# Export all backends
curl -H "Authorization: Bearer token" \
  http://localhost:43411/v1.0/backends > backends.json
```

Import configuration to new instance:

```bash
# Import backends first
cat backends.json | jq '.[]' | while read backend; do
  curl -X PUT -H "Authorization: Bearer token" \
    -H "Content-Type: application/json" \
    -d "$backend" \
    http://new-host:43411/v1.0/backends
done

# Then import frontends
cat frontends.json | jq '.[]' | while read frontend; do
  curl -X PUT -H "Authorization: Bearer token" \
    -H "Content-Type: application/json" \
    -d "$frontend" \
    http://new-host:43411/v1.0/frontends
done
```

## Next Steps

* Review [API Reference](api-reference.md) for programmatic configuration
* Explore [Deployment Options](deployment-options.md) for your infrastructure
* Check [Monitoring and Observability](monitoring.md) for production insights
