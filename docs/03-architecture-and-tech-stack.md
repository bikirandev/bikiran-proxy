# 03 - Architecture & Tech Stack

## 4. Architecture & Tech Stack

### 4.1 High-Level Architecture

The application runs as a single Docker container containing both the proxy engine and the management UI.

1.  **The Edge Proxy (Middleware):** Intercepts all incoming requests. It matches paths, selects a backend target based on the chosen strategy (Round Robin, Random, etc.), and rewrites the URL.
2.  **The Control Plane (Background Services):** Handles health checks to backend services and updates the internal state (available nodes).
3.  **The Configuration Loader:** Reads and validates JSON configuration file, supports hot-reload via file watching.
4.  **The Logging System:** Writes error and access logs to hourly-rotated files with structured format.

### 4.2 Component Architecture

```
┌─────────────────────────────────────────────────┐
│                   Client                        │
└─────────────────┬───────────────────────────────┘
                  │ HTTPS Request
                  ▼
┌─────────────────────────────────────────────────┐
│           Next.js Middleware Layer              │
│  ┌──────────────────────────────────────────┐   │
│  │  Request Interceptor & Router            │   │
│  │  - Path matching                         │   │
│  │  - Load balancing strategy selection     │   │
│  │  - Header manipulation                   │   │
│  │  - Error logging to files                │   │
│  └──────────────────────────────────────────┘   │
└─────────────────┬───────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────┐
│            Backend Selector                     │
│  ┌──────────────────────────────────────────┐   │
│  │  Health State Manager (Redis/Memory)     │   │
│  │  - Active backends list                  │   │
│  │  - Connection counts                     │   │
│  └──────────────────────────────────────────┘   │
└─────────────────┬───────────────────────────────┘
                  │
    ┌─────────────┼─────────────┐
    ▼             ▼             ▼
┌────────┐   ┌────────┐   ┌────────┐
│Backend1│   │Backend2│   │Backend3│
└────────┘   └────────┘   └────────┘

┌─────────────────────────────────────────────────┐
│       Configuration & Logging Layer             │
│  ┌──────────────────────────────────────────┐   │
│  │  JSON Config File (config.json)          │   │
│  │  - Backend servers                       │   │
│  │  - Load balancing strategy               │   │
│  │  - Health check settings                 │   │
│  └──────────────────────────────────────────┘   │
│  ┌──────────────────────────────────────────┐   │
│  │  File Logger (log_yyyy_mm_dd_HH.log)     │   │
│  │  - Error logs                            │   │
│  │  - Health check failures                 │   │
│  │  - Optional access logs                  │   │
│  └──────────────────────────────────────────┘   │
└─────────────────────────────────────────────────┘
```

### 4.3 Tech Stack

**Core Framework**

- **Framework:** Next.js 15.1+ (App Router with React Server Components)
- **Language:** TypeScript 5.x (strict mode)
- **Runtime:** Node.js 20 LTS
- **Proxy Logic:** Next.js Middleware (`middleware.ts`)

**Backend & Data**

- **Configuration:** JSON file with Zod validation
- **File Watching:** chokidar for config hot-reload
- **State Store:**
  - _Development:_ In-memory (global variables)
  - _Production:_ Redis 7+ (optional, for multi-instance)
- **Logging:** Custom file-based logger with hourly rotation
- **Metrics:** In-memory metrics collector

**DevOps & Infrastructure**

- **Containerization:** Docker (Alpine-based multi-stage builds)
- **Orchestration:** Docker Compose (dev), Kubernetes (production)
- **CI/CD:** GitHub Actions
- **Testing:** Jest + Playwright
- **Code Quality:** ESLint, Prettier, Husky

**Security**

- **Rate Limiting:** upstash/ratelimit or custom implementation
- **Secrets Management:** Docker secrets / Environment variables
