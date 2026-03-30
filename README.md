# ⚙️ Neon — Distributed Scientific Compute Platform (DSCP)

<p align="center">
  <img src="public/readme_icon.png" alt="Neon project icon" width="180" />
</p>

Neon is a distributed computing platform for scientific simulations and data processing.  
Think of it as a **mini-Slurm + mini-Kubernetes + job queue + simulation engine**, built in **Go**.

It is designed to schedule, execute, monitor, and recover computational workloads across a cluster of worker nodes, with strong focus on reliability, observability, and performance.

---

## Table of Contents

- [Why Neon?](#why-neon)
- [Core Capabilities](#core-capabilities)
- [Architecture](#architecture)
  - [1) Master Node (Control Plane)](#1-master-node-control-plane)
  - [2) Worker Nodes](#2-worker-nodes)
  - [3) Job System](#3-job-system)
- [Networking Layer](#networking-layer)
- [Monitoring and Dashboard](#monitoring-and-dashboard)
- [Fault Tolerance and Reliability](#fault-tolerance-and-reliability)
- [Security Model](#security-model)
- [Execution and Isolation](#execution-and-isolation)
- [Scalability Strategy](#scalability-strategy)
- [Suggested Technology Stack](#suggested-technology-stack)
- [Example Scientific Workloads](#example-scientific-workloads)
- [End-to-End Workflow](#end-to-end-workflow)
- [Project Structure (Planned)](#project-structure-planned)
- [Configuration (Planned)](#configuration-planned)
- [API Surfaces (Planned)](#api-surfaces-planned)
- [Quick Start (Current Repository State)](#quick-start-current-repository-state)
- [Development Roadmap](#development-roadmap)
- [Testing and Quality Strategy](#testing-and-quality-strategy)
- [CV / Career Value](#cv--career-value)
- [Contributing](#contributing)
- [License](#license)

---

## Why Neon?

Scientific and technical workloads often require:

- high throughput job scheduling,
- predictable execution and retry behavior,
- resource-aware placement (CPU/RAM/GPU),
- resilient infrastructure that survives node failures,
- real-time observability of jobs and cluster health.

Neon targets exactly that space: **distributed, fault-tolerant compute orchestration** for simulations and batch data pipelines.

---

## Core Capabilities

Neon is intended to support:

- **Job definition** for simulation, processing, and batch workloads
- **Cluster submission** via API
- **Automated load distribution** across worker nodes
- **Resource monitoring** (CPU, RAM, optional GPU)
- **Result aggregation** and streaming
- **Fault tolerance** with retries and health-aware scheduling
- **Live operational visibility** through metrics, logs, and UI dashboard

---

## Architecture

### 1) Master Node (Control Plane)

The Master Node acts as the orchestration brain:

- Scheduler
- Worker registry
- Heartbeat management
- Load balancing
- Central job queue
- REST + gRPC APIs
- Authentication and authorization (JWT and/or mTLS)

Primary responsibilities:

1. Accept jobs from clients
2. Validate and enqueue workloads
3. Select suitable workers based on resource and health state
4. Track execution lifecycle
5. Aggregate status, logs, and final outputs

---

### 2) Worker Nodes

Worker Nodes execute jobs and report state:

- Register with the master
- Report resource usage and availability
- Run tasks in isolated sandboxes
- Enforce timeouts and cancellation
- Apply retry logic where applicable
- Stream logs and partial/final results

Worker design goals:

- deterministic execution lifecycle,
- strict resource limits,
- graceful failure handling,
- reconnect support after network interruptions.

---

### 3) Job System

Supported job categories:

- **Batch Job** — single workload unit
- **Parallel Job (Map-Reduce style)** — fan-out/fan-in pipelines
- **Streaming Job** — continuous or chunked data processing

Scientific examples:

- cellular automata simulation,
- differential equation solver,
- Monte Carlo simulation,
- measurement data analysis pipelines.

---

## Networking Layer

Proposed communication model:

- **gRPC** for Master ↔ Worker communication
- **WebSocket** for live dashboard updates
- Optional custom protocol extensions for advanced scenarios

Network reliability features:

- health checks
- reconnect logic
- worker discovery (e.g., gossip-based membership)

---

## Monitoring and Dashboard

Neon should provide both machine and human observability:

- Live cluster load charts
- Job list and execution state
- Cluster health panel
- Real-time log streaming

Backend observability components:

- Prometheus exporter
- Structured logging
- Distributed tracing

---

## Fault Tolerance and Reliability

Reliability mechanisms include:

- heartbeat-driven worker liveness detection,
- automatic rescheduling on worker failure,
- configurable retry policies,
- timeout and cancellation enforcement,
- queue persistence strategy (to prevent job loss),
- optional replicated control plane (Raft-inspired design).

Desired outcomes:

- no silent job drops,
- bounded recovery times,
- transparent failure reporting.

---

## Security Model

Security should be first-class in production scenarios:

- API authentication (JWT)
- Service-to-service trust (mTLS)
- Role-based authorization for admin/operator/client paths
- Request validation and schema checks
- Auditable logs for control-plane actions

---

## Execution and Isolation

To safely execute arbitrary scientific workloads:

- containerized task sandboxing (Docker API or equivalent)
- namespace-based isolation
- CPU/memory limits and quotas
- optional GPU assignment constraints

This model reduces blast radius and improves reproducibility.

---

## Scalability Strategy

Neon can scale through:

- horizontal worker expansion,
- adaptive scheduling under varying load,
- dynamic worker provisioning (manual or policy-driven),
- plugin system for compute module extension.

---

## Suggested Technology Stack

- **Language:** Go
- **RPC:** gRPC
- **HTTP API:** REST
- **Realtime:** WebSocket
- **Metrics:** Prometheus
- **Tracing:** OpenTelemetry-compatible stack
- **Storage (optional):** PostgreSQL / Redis / object store depending on persistence model
- **Isolation:** Docker + Linux namespaces/cgroups

---

## Example Scientific Workloads

1. **Cellular Automata**
   - Parameter sweep across initial states
   - Parallel execution over worker pool

2. **Monte Carlo Engine**
   - Millions of independent random trials
   - Aggregation of confidence intervals

3. **Differential Solver Batch**
   - Input parameter scans
   - Numerical solver output collection

4. **Measurement Pipeline**
   - Streaming ingestion
   - Filter/transform/analyze/report stages

---

## End-to-End Workflow

1. Client submits job definition via REST/gRPC.
2. Master validates and enqueues the job.
3. Scheduler selects one or more eligible workers.
4. Workers execute workload in isolated runtime.
5. Workers stream logs/progress; master updates state.
6. On failure: retry/reschedule based on policy.
7. On completion: results are aggregated and exposed to client/UI.

---

## Project Structure (Planned)

As implementation grows, a structure like this is recommended:

```text
Neon/
├── cmd/
│   ├── master/           # Control plane entrypoint
│   ├── worker/           # Worker node entrypoint
│   └── cli/              # Optional admin/client CLI
├── internal/
│   ├── scheduler/
│   ├── queue/
│   ├── registry/
│   ├── heartbeat/
│   ├── executor/
│   ├── sandbox/
│   └── auth/
├── api/
│   ├── proto/            # gRPC contracts
│   └── openapi/          # REST contracts
├── web/                  # Dashboard frontend
├── deploy/               # Docker/K8s/deployment assets
├── docs/                 # Technical docs
└── tests/                # Integration and e2e tests
```

---

## Configuration (Planned)

Typical configurable domains:

- Cluster topology and node identity
- Scheduler strategy and queue parameters
- Retry/timeouts/dead-letter behavior
- Resource limits (CPU/RAM/GPU)
- Security settings (JWT secrets, cert paths, mTLS mode)
- Metrics/tracing export endpoints

---

## API Surfaces (Planned)

- **REST API** for user-facing job operations and status queries
- **gRPC API** for high-performance master/worker coordination
- **WebSocket stream** for live telemetry, logs, and state transitions

Expected API domains:

- Job submission/cancellation
- Job status and results retrieval
- Worker registration and heartbeat endpoints
- Cluster status and operational diagnostics

---

## Quick Start (Current Repository State)

This repository is currently at an early stage and mainly contains project assets.  
Implementation modules, runtime services, and deployment scripts are expected to be added incrementally.

Recommended next steps:

1. Bootstrap `master` and `worker` service binaries in Go.
2. Define gRPC contracts for registration, heartbeat, and execution.
3. Implement minimal in-memory queue + round-robin scheduler.
4. Add one reference scientific job (e.g., Monte Carlo π estimation).
5. Expose metrics and basic WebSocket live status.

---

## Development Roadmap

### Phase 1 — Foundations

- Core master service
- Worker registration + heartbeats
- Basic queue and scheduling
- Single batch job support

### Phase 2 — Reliability and Observability

- Retry/timeout policies
- Structured logs + metrics + tracing
- Improved failure reporting

### Phase 3 — Advanced Execution

- Parallel and streaming jobs
- Result aggregation pipelines
- Resource-aware scheduling improvements

### Phase 4 — Production Features

- mTLS and stronger auth policies
- Replicated control plane options
- Dynamic scaling and plugin architecture

---

## Testing and Quality Strategy

Recommended testing layers:

- Unit tests for scheduler, queue, and retry logic
- Integration tests for master-worker interactions
- Fault-injection tests (worker crash, network partition, timeout)
- Performance profiling (pprof) under synthetic workload
- End-to-end scenario tests with representative scientific jobs

---

## CV / Career Value

Neon demonstrates practical capability in:

- computer networking,
- Go concurrency (goroutines, channels, worker pools),
- distributed systems design,
- fault tolerance engineering,
- backend architecture,
- DevOps and observability,
- performance tuning and profiling,
- integration testing for real infrastructure.

This is not a trivial demo app.  
It is infrastructure-grade engineering with direct relevance to:

- technical physics,
- scientific data processing,
- HPC-oriented systems,
- backend platform engineering,
- distributed compute and operations.

---

## Contributing

Contributions are welcome once the initial implementation baseline is in place.

General expectations:

- keep changes focused and incremental,
- include tests for behavior changes,
- document public-facing interfaces,
- preserve backward compatibility where applicable.
