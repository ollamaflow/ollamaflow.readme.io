---
title: Deployment Options
excerpt: >-
  OllamaFlow supports multiple deployment methods to fit different  
  infrastructure requirements and preferences. Choose the option that best
  suits   your environment.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Docker Deployment

Docker deployment is the recommended approach for most users, providing consistent runtime environments and simplified management.

### Single Container

The simplest deployment runs OllamaFlow as a single container:

```bash
# Basic deployment with persistent data
docker run -d \
  --name ollamaflow \
  --restart unless-stopped \
  -p 43411:43411 \
  -v /opt/ollamaflow/ollamaflow.json:/app/ollamaflow.json \
  -v /opt/ollamaflow/ollamaflow.db:/app/ollamaflow.db \
  jchristn/ollamaflow:v1.1.0
```

### Docker Compose

For complex deployments, use Docker Compose:

```yaml
# docker-compose.yml
version: '3.8'

services:
  ollamaflow:
    image: jchristn/ollamaflow:v1.1.0
    container_name: ollamaflow
    restart: unless-stopped
    ports:
      - "43411:43411"
    volumes:
      - ./ollamaflow.json:/app/ollamaflow.json:ro
      - ./ollamaflow.db:/app/ollamaflow.db
      - ./logs/:/app/logs/
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:43411/"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

Deploy with:

```bash
# Start the deployment
docker-compose up -d

# View logs
docker-compose logs -f ollamaflow

# Stop the deployment
docker-compose down
```

<br />

```bash
# Download the latest release (replace with actual release URL)
wget https://github.com/jchristn/ollamaflow/releases/latest/download/ollamaflow-linux-x64.tar.gz

# Extract
tar -xzf ollamaflow-linux-x64.tar.gz

# Move to installation directory
sudo mv ollamaflow /opt/

# Create user and set permissions
sudo useradd -r -s /bin/false ollamaflow
sudo chown -R ollamaflow:ollamaflow /opt/ollamaflow
sudo chmod +x /opt/ollamaflow/OllamaFlow.Server
```

## Source Code Deployment

Deploy from source for development or customization.

### Prerequisites

* .NET 8.0 SDK
* Git

### Build and Deploy

```bash
# Clone repository
git clone https://github.com/ollamaflow/ollamaflow.git
cd ollamaflow

# Build release version
dotnet build src/OllamaFlow.sln -c Release

# Publish self-contained application
dotnet publish src/OllamaFlow.Server -c Release -r linux-x64 --self-contained

# Deploy binaries
sudo cp -r src/OllamaFlow.Server/bin/Release/net8.0/linux-x64/publish /opt/ollamaflow
sudo chown -R ollamaflow:ollamaflow /opt/ollamaflow
```

### Development Mode

For development environments:

```bash
cd src/OllamaFlow.Server
dotnet run
```

## Kubernetes Deployment

Deploy OllamaFlow in Kubernetes environments.

### Basic Deployment

```yaml
# ollamaflow-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ollamaflow
  labels:
    app: ollamaflow
spec:
  replicas: 2
  selector:
    matchLabels:
      app: ollamaflow
  template:
    metadata:
      labels:
        app: ollamaflow
    spec:
      containers:
      - name: ollamaflow
        image: jchristn/ollamaflow:v1.1.0
        ports:
        - containerPort: 43411
        env:
        - name: ASPNETCORE_ENVIRONMENT
          value: "Production"
        volumeMounts:
        - name: config
          mountPath: /app/ollamaflow.json
          subPath: ollamaflow.json
          readOnly: true
        - name: database
          mountPath: /app/ollamaflow.db
          subPath: ollamaflow.db
          readOnly: false
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "1Gi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /
            port: 43411
          initialDelaySeconds: 30
          periodSeconds: 30
        readinessProbe:
          httpGet:
            path: /
            port: 43411
          initialDelaySeconds: 5
          periodSeconds: 10
      volumes:
      - name: config
        configMap:
          name: ollamaflow-config
      - name: data
        persistentVolumeClaim:
          claimName: ollamaflow-data

---
apiVersion: v1
kind: Service
metadata:
  name: ollamaflow-service
spec:
  selector:
    app: ollamaflow
  ports:
  - protocol: TCP
    port: 43411
    targetPort: 43411
  type: LoadBalancer

---
apiVersion: v1
kind: ConfigMap
metadata:
  name: ollamaflow-config
data:
  ollamaflow.json: |
    {
      "Webserver": {
        "Hostname": "*",
        "Port": 43411
      },
      "Logging": {
        "ConsoleLogging": true,
        "MinimumSeverity": "Info"
      },
      "AdminBearerTokens": [
        "kubernetes-admin-token"
      ]
    }

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ollamaflow-data
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Deploy to Kubernetes:

```bash
# Apply the deployment
kubectl apply -f ollamaflow-deployment.yaml

# Check deployment status
kubectl get deployments
kubectl get pods
kubectl get services

# View logs
kubectl logs -l app=ollamaflow
```

## Load Balancer and Reverse Proxy

Configure external load balancers or reverse proxies.

### Nginx Configuration

```nginx
# /etc/nginx/sites-available/ollamaflow
upstream ollamaflow_backend {
    server 127.0.0.1:43411 max_fails=3 fail_timeout=30s;
    # Add more OllamaFlow instances for high availability
    # server 127.0.0.1:43412 max_fails=3 fail_timeout=30s;
}

server {
    listen 80;
    server_name ai.yourcompany.com;

    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name ai.yourcompany.com;

    # SSL configuration
    ssl_certificate /etc/ssl/certs/yourcompany.crt;
    ssl_certificate_key /etc/ssl/private/yourcompany.key;

    # Proxy settings
    location / {
        proxy_pass http://ollamaflow_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Streaming support
        proxy_buffering off;
        proxy_cache off;

        # Timeout settings
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 300s;
    }

    # Health check endpoint
    location /health {
        access_log off;
        proxy_pass http://ollamaflow_backend/;
    }
}
```

### HAProxy Configuration

```haproxy
# /etc/haproxy/haproxy.cfg
global
    log stdout local0
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    mode http
    log global
    option httplog
    option dontlognull
    timeout connect 5000
    timeout client 300000
    timeout server 300000

frontend ollamaflow_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/yourcompany.pem
    redirect scheme https if !{ ssl_fc }

    default_backend ollamaflow_backend

backend ollamaflow_backend
    balance roundrobin
    option httpchk GET /

    server ollamaflow1 127.0.0.1:43411 check
    # server ollamaflow2 127.0.0.1:43412 check

listen stats
    bind *:8404
    stats enable
    stats uri /stats
    stats refresh 30s
```

## Security Considerations

### Firewall Configuration

```bash
# Allow OllamaFlow port
sudo ufw allow 43411/tcp

# Restrict admin API access (optional)
sudo ufw allow from 192.168.1.0/24 to any port 43411
```

### SSL/TLS Termination

For production deployments, always use SSL/TLS:

1. **At Load Balancer**: Terminate SSL at nginx/HAProxy (recommended)
2. **At Application**: Configure OllamaFlow with SSL certificates
3. **End-to-End**: Use SSL throughout the chain

### Authentication

* Change default admin bearer tokens
* Use strong, randomly generated tokens
* Rotate tokens regularly
* Consider integrating with existing authentication systems

## Monitoring and Logging

### Health Checks

Configure health check endpoints:

```bash
# Application health
curl -f http://localhost:43411/ || exit 1

# Backend health
curl -H "Authorization: Bearer your-token" \
  http://localhost:43411/v1.0/backends/health
```

### Log Management

Configure log rotation and management:

```bash
# Logrotate configuration
sudo tee /etc/logrotate.d/ollamaflow > /dev/null << 'EOF'
/var/log/ollamaflow/*.log {
    daily
    missingok
    rotate 30
    compress
    notifempty
    create 0644 ollamaflow ollamaflow
    postrotate
        systemctl reload ollamaflow
    endscript
}
EOF
```

## Next Steps

* Review [Configuration Reference](configuration-reference.md) for detailed settings
* Explore [API Documentation](api-reference.md) for integration
* Check [Monitoring and Observability](monitoring.md) for production insights

```bash
# Logrotate configuration
sudo tee /etc/logrotate.d/ollamaflow > /dev/null << 'EOF'
/var/log/ollamaflow/*.log {
    daily
    missingok
    rotate 30
    compress
    notifempty
    create 0644 ollamaflow ollamaflow
    postrotate
        systemctl reload ollamaflow
    endscript
}
EOF
```

<br />
