# 04 - Data Models & Configuration Format

## 5. Data Models & Configuration Format

### 5.1 Configuration File Structure (config.json)

```json
{
  "proxy": {
    "port": 3000,
    "timeout": 30000,
    "retries": 3
  },
  "loadBalancing": {
    "strategy": "round-robin",
    "stickySession": {
      "enabled": false,
      "cookieName": "BACKEND_ID",
      "ttl": 3600
    }
  },
  "backends": [
    {
      "id": "backend-1",
      "url": "http://api1.example.com",
      "weight": 1,
      "healthCheck": {
        "enabled": true,
        "path": "/health",
        "interval": 10000,
        "timeout": 5000,
        "healthyThreshold": 2,
        "unhealthyThreshold": 3
      }
    },
    {
      "id": "backend-2",
      "url": "http://api2.example.com",
      "weight": 1,
      "healthCheck": {
        "enabled": true,
        "path": "/health",
        "interval": 10000,
        "timeout": 5000,
        "healthyThreshold": 2,
        "unhealthyThreshold": 3
      }
    }
  ],
  "rateLimit": {
    "enabled": true,
    "windowMs": 60000,
    "maxRequests": 100
  },
  "logging": {
    "errorLog": {
      "enabled": true,
      "directory": "./logs",
      "filePattern": "log_yyyy_mm_dd_HH.log",
      "level": "error"
    },
    "accessLog": {
      "enabled": false,
      "directory": "./logs",
      "filePattern": "access_yyyy_mm_dd_HH.log"
    }
  }
}
```

### 5.2 Core Data Structures

```typescript
interface BackendServer {
  id: string;
  url: string;
  weight: number;
  healthCheck: {
    path: string;
    interval: number;
    timeout: number;
    healthyThreshold: number;
    unhealthyThreshold: number;
  };
  status: "healthy" | "unhealthy" | "unknown";
  lastCheck: Date;
  consecutiveFailures: number;
}

interface LoadBalancingStrategy {
  type: "round-robin" | "random" | "ip-hash" | "least-connections";
  stickySession?: {
    enabled: boolean;
    cookieName: string;
    ttl: number;
  };
}

interface ProxyConfig {
  backends: BackendServer[];
  strategy: LoadBalancingStrategy;
  timeout: number;
  retries: number;
  rateLimit?: {
    windowMs: number;
    maxRequests: number;
  };
}

interface RequestMetric {
  timestamp: Date;
  method: string;
  path: string;
  backendId: string;
  statusCode: number;
  duration: number;
  clientIp: string;
}
```

### 5.3 Log File Format

**Error Log Entry (JSON per line):**

```json
{
  "timestamp": "2026-01-13T10:30:45.123Z",
  "level": "error",
  "type": "backend_failure",
  "backendId": "backend-1",
  "url": "http://api1.example.com",
  "error": "ETIMEDOUT",
  "message": "Backend connection timeout",
  "clientIp": "192.168.1.100"
}
```

**Access Log Entry (optional):**

```json
{
  "timestamp": "2026-01-13T10:30:45.123Z",
  "method": "GET",
  "path": "/api/users",
  "backendId": "backend-2",
  "statusCode": 200,
  "duration": 45,
  "clientIp": "192.168.1.100"
}
```
