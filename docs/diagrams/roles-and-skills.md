## Components and roles
- Mobile App (iOS/Android)
    - Mobile engineer (iOS/Android)
    - QA (testing on real devices, OS versions, network conditions)
    - DevOps (CI/CD, app store deployment, crash monitoring)
- MTProto Gateway (DC Entry)
    - Backend engineer (C++/Go, low-level networking, TLS/MTProto)
    - Security engineer (encryption, DDoS mitigation, protocol hardening)
    - DevOps (edge deployment, load balancing, health checks across DCs)
- Message Handling Service
    - Backend engineer (distributed systems, message routing, consistency)
    - SRE (latency/throughput SLIs, auto-scaling, sharding logic)
    - QA (end-to-end message delivery testing, failure injection)
- Auth & Session Service
    - Backend engineer (auth protocols, session management, multi-device sync)
    - Security engineer (2FA, device binding, token revocation, breach detection)
    - DevOps (high-availability setup, secret rotation, audit logging)
- State Cache (Redis)
    - DevOps (cluster provisioning, replication, failover, memory tuning)
    - Backend engineer (cache invalidation strategy, TTL policies, fallback logic)
    - SRE (latency monitoring, eviction analysis, Redis module integration)

## Roles and responsibilities
