# Karkain Network Monitor

A CLI/TUI/service-first active-check monitor built exclusively on Karkain Language v1.2.0.

## Status

| Item | Status |
|---|---|
| Research | COMPLETE |
| Product design | COMPLETE |
| Repository creation | COMPLETE |
| Implementation | NOT STARTED |
| Compile/execute verification of the application | NOT STARTED (toolchain itself VERIFIED on Karkain v1.2.0) |

## What it does

Karkain Network Monitor runs declarative active checks over TCP and HTTP, evaluates local thresholds, emits text/JSON alert events and keeps check history in an embedded store. It covers health, latency from TCP/HTTP timing, threshold breaches and alerting — the polling and observability side of networking. It is distinct from Karkain Network Manager, which owns device inventory, topology and configuration. Datagram-based capabilities (SNMP/ICMP packet loss, NetFlow/sFlow traffic, syslog) are NOT available in Karkain v1.2.0 and are recorded as blockers; this product is therefore positioned as a composable probe that can feed Zabbix or PRTG, not as a replacement for them.

## Who it serves

It serves constrained and edge sites, airflow-gapped or text-only operations, and engineers who want a lightweight, database-free probe with no per-sensor fees. Teams that already run a full monitoring platform but need a scriptable local checker are a secondary audience.

## Planned features

All items below are **(planned)** and are specification targets, not implemented behaviour.

- **Check registry (planned):** declarative TCP-connect and HTTP GET checks with interval and timeout settings.
- **Polling loop (planned):** a single sequential scheduler; concurrent multi-target polling is BLOCKED.
- **Health/latency (planned):** reachability plus timing derived from TCP connect and HTTP response using Karkain-native timing (PARTIAL `time` — must be probed).
- **Thresholds (planned):** local, per-check thresholds with severity levels and hysteresis.
- **Alerts (planned):** append-only text/JSON event log and stdout events; WebSocket push is BLOCKED.
- **History (planned):** embedded `db2` time-series of check results with retention.
- **Traffic/flow (planned, constrained):** only HTTP/TCP-derived counters; UDP/NetFlow/sFlow ingest is BLOCKED.
- **CLI/TUI dashboard (planned):** text tables, JSON mode and service-mode event output; no GUI.

## Explicit non-goals

- Not a network device inventory/topology/configuration manager (Karkain Network Manager).
- Not a server/application manager (Karkain Server Manager).
- Not an API gateway (Karkain API Manager).
- No UDP/SNMP/ICMP packet loss, no NetFlow/sFlow traffic ingest, no TLS, no WebSocket, no concurrent poller, no native GUI.
- No modification of the Karkain language, compiler or standard library.

## Karkain foundation

Exclusive foundation: Karkain Language v1.2.0. All application source will be Karkain. No other implementation language. No compiler modifications.

## Platform support status

- Windows x86-64 toolchain: **VERIFIED** (Karkain v1.2.0 compiles and runs a native executable on this host).
- Linux cross-target output: **PRESENT but execution NOT VERIFIED** (cross-compilation output exists; it was not executed on this host).
- macOS: **NOT VERIFIED**.

## Build and run status

No application binary exists yet. The intended generic commands, which become active when implementation starts, are:

```
karkain build src/main.kark -o <name>
karkain run src/main.kark
```

These are placeholders for the planned source layout, not verified commands for this application.

## Known blockers

- **UDP/datagram** — no SNMP/ICMP, NetFlow/sFlow/IPFIX or syslog ingest (BLOCKED).
- **Concurrent poller** — only sequential TCP/HTTP building blocks; no multi-target concurrency (BLOCKED).
- **TLS** — no secure monitor transport or authenticated upstream (BLOCKED).
- **WebSocket** — no push-based live events (BLOCKED).
- **GUI** — no native GUI toolkit; GUI is a planned, blocked layer.
- **OS metric/process APIs** — no host-level CPU/memory/process metrics (BLOCKED/PARTIAL).

## Repository

- Source: https://github.com/ajit-ai/Karkain-Network-Monitor
- Issues: https://github.com/ajit-ai/Karkain-Network-Monitor/issues
- License: Apache-2.0
