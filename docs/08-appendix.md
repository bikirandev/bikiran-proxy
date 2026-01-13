# 08 - Appendix

## 14. Appendix

### 14.1 Glossary

- **Backend:** Upstream server that handles proxied requests
- **Health Check:** Periodic test to determine backend availability
- **Load Balancing Strategy:** Algorithm for selecting which backend to route requests to
- **Sticky Session:** Routing all requests from a client to the same backend
- **Circuit Breaker:** Pattern to prevent cascading failures
- **Hot Reload:** Configuration changes applied without restarting the service
- **Hourly Log Rotation:** Automatic creation of new log files every hour

### 14.2 References

- Next.js Middleware Documentation: https://nextjs.org/docs/app/building-your-application/routing/middleware
- HTTP Proxy Standards: RFC 7230-7235
- Load Balancing Algorithms: NGINX documentation
- Container Best Practices: Docker documentation
- Zod Schema Validation: https://zod.dev
- Chokidar File Watching: https://github.com/paulmillr/chokidar

### 14.3 Decision Log

- **ADR-001:** Chose Next.js for unified frontend/backend codebase
- **ADR-002:** Selected Redis as optional state store for scalability
- **ADR-003:** Middleware-based approach over custom HTTP server for simplicity
- **ADR-004:** TypeScript strict mode for better reliability
- **ADR-005:** JSON configuration over YAML for better TypeScript integration
- **ADR-006:** File-based logging over external log aggregators for simplicity
- **ADR-007:** Hourly log rotation for manageable file sizes and easy archival
- **ADR-008:** No dashboard in v1.0 to reduce complexity and dependencies

### 14.4 Sample Commands

**Run locally with Docker:**

```bash
docker build -t nextproxy:latest .
docker run -p 3000:3000 -v $(pwd)/config.json:/app/config.json -v $(pwd)/logs:/app/logs nextproxy:latest
```

**Analyze logs:**

```bash
# Count errors by type
cat logs/log_2026_01_13_10.log | jq -r '.type' | sort | uniq -c

# Find slow requests
cat logs/access_2026_01_13_10.log | jq 'select(.duration > 1000)'

# Monitor live logs
tail -f logs/log_2026_01_13_$(date +%H).log | jq .
```

**Health check:**

```bash
curl http://localhost:3000/health
```

**Reload configuration:**

```bash
# Simply edit config.json - it will auto-reload
vim config.json
```
