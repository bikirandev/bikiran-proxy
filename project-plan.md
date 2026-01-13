# Project Plan: NextProxy - The Next.js Load Balancer & Dashboard

## 1. Executive Summary

**NextProxy** is an open-source, highly customizable Load Balancer (LB) and Reverse Proxy built entirely within the Next.js ecosystem. Unlike traditional opaque proxies (Nginx, HAProxy), NextProxy provides a programmable JavaScript/TypeScript interface for routing logic and includes a built-in, real-time dashboard to visualize traffic and backend health.

**Core Value Proposition:** "A Load Balancer you can hack, with a Dashboard you don't need to build."

## 2. Architecture & Tech Stack

### 2.1 High-Level Architecture

The application runs as a single Docker container containing both the proxy engine and the management UI.

1.  **The Edge Proxy (Middleware):** Intercepts all incoming requests. It matches paths, selects a backend target based on the chosen strategy (Round Robin, Random, etc.), and rewrites the URL.
2.  **The Control Plane (API Routes):** Handles health checks to backend services and updates the internal state (available nodes).
3.  **The Dashboard (Frontend):** A React-based UI (Next.js App Router) to view real-time metrics, logs, and configure backends.

### 2.2 Tech Stack

- **Framework:** Next.js 16+ (App Router)
- **Language:** TypeScript
- **Proxy Logic:** Next.js Middleware (`middleware.ts`)
- **UI Components:** Tailwind CSS 4+ for styling, React for interactivity
- **State Management:** \* _Stateless Mode:_ Cookies / Hash-based routing.
  - _Stateful Mode:_ Redis (optional) or Global Node variables (for single-container deployments).
- **Containerization:** Docker (Multi-stage builds)

## 3. Implementation Phases

### Phase 1: The MVP (Skeleton)

**Goal:** A working proxy that distributes traffic between two hardcoded URLs using a Random strategy.

- [ ] **Setup**: Initialize Next.js project with TypeScript.
- [ ] **Proxy Engine**: Create `middleware.ts` to intercept `/*`.
  - Implement logic to rewrite requests to upstream servers.
  - Handle `X-Forwarded-For` headers.
- [ ] **Config System**: Create a `config.ts` or `.env` loader to define backend servers.
- [ ] **Docker**: Create a production-optimized `Dockerfile`.

### Phase 2: Intelligence & Health (The Logic)

**Goal:** Active health checking and smarter load balancing.

- [ ] **Health Checker**: Create a Cron job (or `setInterval` in a custom server entry) to ping backends every 10s.
- [ ] **State Store**: Implement a Singleton service to hold the list of _healthy_ backends.
- [ ] **Strategies**: Implement Round Robin and IP-Hash algorithms.
- [ ] **Logging**: Implement a structured logger that feeds into the Dashboard API.

### Phase 3: The Dashboard (The Interface)

**Goal:** Visual management of the system.

- [ ] **Real-time Status**: UI showing active backends with Green/Red indicators.
- [ ] **Metrics**: Simple charts (Recharts/Visx) showing Requests Per Second (RPS).
- [ ] **Live Logs**: A scrolling terminal-like window showing incoming requests in real-time.

### Phase 4: Open Source Optimization

**Goal:** Prepare for community contribution.

- [ ] **Documentation**: Write a clear `README.md` and `CONTRIBUTING.md`.
- [ ] **CI/CD**: GitHub Actions for linting, type-checking, and Docker build verification.
- [ ] **Release**: Publish to Docker Hub.
