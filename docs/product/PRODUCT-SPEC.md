# Karkain Network Monitor — Product Specification

**Document status:** Design COMPLETE. Research = COMPLETE. Repository creation = COMPLETE. Application implementation = NOT STARTED. Application compile/execute verification = NOT STARTED (toolchain path itself VERIFIED on Karkain v1.2.0).


## A. Product definition

**Product name:** Karkain Network Monitor.

**Purpose and problem:** Constrained and edge sites need a text-native monitor without a database server, GUI stack or per-sensor licensing. Karkain v1.2.0 provides TCP and HTTP building blocks but no UDP, TLS, WebSocket, concurrent server framework or OS metric APIs. This product therefore delivers declarative active TCP/HTTP checks, local thresholds, text/JSON alerts and embedded history — and positions these honestly as a composable probe feeding larger platforms, within those limits.

**Vision:** A lightweight, scriptable check runner whose output is stable text/JSON events and a queryable local history, implemented solely in Karkain v1.2.0.

**Intended users and segments:** Operators of air-gapped, edge or lab sites; engineers wanting a local pre-flight checker; teams that run Zabbix/PRTG and need a small composable probe; developers testing service reachability in CI-like text workflows.

**Usage scenarios:** (1) Declare a TCP-connect check and an HTTP GET check. (2) Run one polling pass and view reachability and latency as text. (3) Define a latency threshold and see an alert event when breached. (4) Query check history. (5) Emit JSON events for ingestion by Zabbix or PRTG.

**Market positioning:** A CLI/TUI-first, database-free active checker — a complement to, not a replacement for, full monitoring platforms. It occupies the "text-native probe" gap.

**Primary value proposition:** No per-sensor fees, no database, no GUI, and composable text/JSON output — with the explicit caveat that UDP-based SNMP/ICMP/flow ingest, TLS, concurrency and WebSocket are BLOCKED and must be cleared at the language level before broader monitoring claims.

**Explicit non-goals and boundaries:** No device inventory/topology/configuration (Karkain Network Manager), no server management (Karkain Server Manager), no API gateway (Karkain API Manager). No UDP/SNMP/ICMP packet loss, NetFlow/sFlow traffic, TLS, WebSocket, concurrent poller or GUI. No language/compiler modification.

## B. Functional architecture

| Module | Responsibility |
|---|---|
| Check Registry | Declarative TCP-connect and HTTP GET checks with interval/timeout. |
| Poller Scheduler | Single sequential pass over registered checks; concurrency BLOCKED. |
| Health Sampler | Reachability and timing from TCP connect / HTTP response (PARTIAL `time`). |
| Threshold Engine | Per-check thresholds, severity levels and hysteresis. |
| Alert Emitter | Append-only text/JSON event log and stdout events; push BLOCKED. |
| History Store | Embedded `db2` time-series of check results with retention. |
| Traffic Counters | HTTP/TCP-derived counters only; UDP flow ingest BLOCKED. |
| Config Loader | Declarative text config via `config`/`io`; validated before use. |
| CLI/TUI Frontend | Command parsing, text tables, JSON mode, service-mode output. |

**Core workflows:** Define checks → configure thresholds → run a polling pass → evaluate thresholds → emit events → store results → query history.

**Admin workflows:** Add/remove/disable checks, tune thresholds and intervals, set retention, export events/history.

**Error/recovery workflows:** Unreachable target (TCP 5 s read timeout) yields a failed check event, not a crash; a malformed check definition is rejected at load with field detail; a failed `db2` write triggers rollback; a timing probe that cannot be verified records `latency: unknown` rather than a fabricated number.

**Roles and permissions:** Local roles (read, edit, admin) gate check and threshold changes; no remote authentication is claimed because TLS is BLOCKED.

**Configuration/customization:** File-based declarative config; no database server required.

**Import/export:** Text/simple JSON check definitions and event export; `json` is PARTIAL, so only simple field extraction is assumed.

**Integration requirements:** JSON events suitable for Zabbix/PRTG ingestion; direct syslog/agent transports requiring UDP/TLS are BLOCKED.

## C. Competitor analysis

Researched competitors and sources are in `docs/competitor-analysis/COMPETITORS.md`, derived from portfolio research. Evidence labels: **[VF]** verified fact, **[RI]** research-based interpretation, **[PROP]** Karkain proposal, **[BLOCKED]** unattainable with v1.2.0, **[UA]** unverified assumption.

**Competitors:** Zabbix and PRTG, with LibreNMS-style FOSS as context.

**[VF]** Zabbix is free open-source observability for infrastructure, apps and services — metrics, logs, traces, events — with correlation, alerting and remediation, SNMP poll/trap, IPMI, agents and flexible triggers. PRTG is Paessler's all-in-one IT/OT/IoT monitor built on "sensors" (one measured value, 5–10 per device), with 250+ sensors, maps, alerts, reports, probes and failover. **[RI]** Zabbix is template/API-driven OSS; PRTG is fast-start, sensor-licensed.

**Feature comparison matrix (Karkain = proposed):**

| Capability | Karkain (proposed) | Zabbix | PRTG |
|---|---|---|---|
| CLI/TUI-first (GUI **[BLOCKED]**) | **[PROP]** yes | PHP web UI + API | Web/desktop/mobile UIs |
| No database dependency **[PROP]** | Embedded db2 only | Requires DB | Requires core server |
| Active TCP/HTTP checks | **[PROP]** yes | Yes | Yes (250+ sensors) |
| UDP/SNMP/flow ingest **[BLOCKED]** | No | Yes | Yes |
| TLS transport **[BLOCKED]** | No | Yes | Yes |
| Concurrent multi-target polling **[BLOCKED]** | No | Yes (proxies) | Yes (probes) |
| No per-sensor licensing **[PROP]** | Yes | Free OSS + support | Sensor licensing |

**Platform/deployment comparison [VF]:** Zabbix server runs on Linux/Unix with agents for Linux/Windows, a PHP web UI, proxies and cloud images; PRTG core server is Windows-only with Linux probes, on-prem or Hosted, with a 30-day trial. Karkain is planned as a single Karkain-built CLI/TUI service on the VERIFIED Windows x86-64 toolchain, with other targets unverified.

**Licensing comparison (verified):** **[VF]** Zabbix on-prem is free under AGPL-3.0 (subscriptions are support/cloud offerings). **[VF]** PRTG is a commercial product with official sensor-based pricing; its specific licence name is **UNKNOWN** — not verified on an official page. Karkain Network Monitor is Apache-2.0. No unverified licence is asserted.

**Pricing [VF]:** Zabbix on-prem free under AGPL-3.0; subscriptions $325/month Silver, from $825 Gold, higher tiers custom; cloud from $50/month. PRTG 500 sensors $200/month, 1,000 $358, 2,500 $742, 10,000 $1,642 annually, net VAT; freeware 100 sensors. No prices are invented.

**Strengths/limitations [VF]/[RI]:** Zabbix has no per-device limits and deep customisation but needs a DB/web stack and manual partitioning; PRTG has ready sensors and easy discovery but sensor licensing raises cost and the core is Windows-only. Karkain's strength is zero-dependency text output; its limitation is the BLOCKED primitive set, which prevents SNMP/flow ingestion and concurrent polling.

**Market gaps [RI]:** Constrained/edge sites need a text-native monitor with no DB, no GUI and no per-sensor fees.

**Karkain differentiation [PROP]:** CLI/TUI/service-first monitor with declarative checks, local thresholds and text/JSON events over the existing TCP/HTTP blocks. **[BLOCKED]** no UDP (SNMP/ICMP, NetFlow/sFlow, syslog), no TLS, no concurrent poller, no GUI — operate as a composable probe feeding Zabbix/PRTG.

**Features deliberately not copied:** SNMP/IPMI/JMX agent ecosystems, web UI/maps and dashboards, SNMP traps and discovery, flow/sFlow ingest, proxy/probe fleets, SLA/business-service trees and sensor-based licensing.

## D. Technical architecture

**Application architecture:** A single process with command dispatcher, check/threshold/domain modules and embedded `db2` persistence; a foreground "service mode" loops over checks sequentially.

**Component boundaries:** Check Registry, Poller, Health Sampler, Threshold Engine, Alert Emitter and History Store are separate modules. All network I/O is isolated in the Health Sampler so BLOCKED transports remain confined.

**Process/service boundaries:** One process, one sequential poll pass. No concurrent poller, proxy or probe fleet is claimed.

**UI architecture:** The UI is CLI/TUI/service-first because Karkain v1.2.0 has no native GUI toolkit (BLOCKED). GUI is a planned layer contingent on ecosystem capability and is not in the MVP.

**Domain model:** Check, CheckResult, Threshold, AlertEvent, HistoryPoint, Role.

**Data storage strategy:** Text config for declarative checks plus embedded `db2` for results/history; no external database server.

**API/interface architecture:** Internal Karkain module calls; external interaction via CLI arguments, text tables and JSON event streams. TCP/HTTP are client building blocks only.

**Event/message architecture:** In-process events appended to a log and emitted to stdout; no broker assumed. WebSocket push is BLOCKED.

**Logging/diagnostics/observability:** Structured text/JSON event lines to stdout and an append-only event log; `karkain lint`/`check --format=json` and the `test` runner provide diagnostics.

**Configuration management:** Declarative text via `config`/`io`, validated before activation.

**Plugin/extension architecture:** Declarative check definitions are the extension point; no dynamic plugin loading. Any FFI/OS integration must be a Karkain-compatible native interface, never another language; none is required for MVP.

**Explicit blockers:** UDP, TLS, WebSocket, concurrent server framework, GUI and OS process/metric APIs are BLOCKED and must not be claimed as solved.

## E. Data design

**Main entities:** Check, CheckResult, Threshold, AlertEvent, HistoryPoint, Role.

**Relationships:** Check has many CheckResults; Check has one or more Thresholds; a breached CheckResult produces an AlertEvent; HistoryPoint is a retained projection of a CheckResult.

**Ownership:** Check Registry owns Check; Health Sampler owns CheckResult; Threshold Engine owns Threshold; Alert Emitter owns AlertEvent; History Store owns HistoryPoint.

**Validation:** Check target and type required; interval/timeout positive; thresholds well-formed; duplicate check ids rejected; failed timing probe recorded as `unknown`, never a fabricated value.

**Lifecycle:** Check active→disabled→removed; CheckResult immutable; AlertEvent open→acknowledged (local only); HistoryPoint retained per policy.

**Persistence:** Text config for checks; embedded `db2` for results/history/events. `db_begin`/`db_commit`/`db_rollback` exist; crash-atomicity UNVERIFIED.

**Transactions:** Result writes use explicit begin/commit/rollback; a failed pass leaves prior history intact.

**Backup/restore:** Export config plus `db2` dump; restore into a fresh workspace and revalidate.

**Migration:** Schema version recorded from start; no pre-v1.2.0 compatibility required.

**Import/export formats:** Text and simple JSON check definitions/events; no full JSON schema dependency.

**Audit history:** Append-only configuration-change event with actor/target/timestamp; file-permission protection PARTIAL.

## F. Karkain implementation architecture

**Required capabilities:** Compiler v1.2.0 only; `net` (TCP client), `http` (client building blocks), `db`/`db2`, `io`, `config`, `json` (PARTIAL), `string`, `collections`, `crypto`, `encoding`, `log`, `time` (PARTIAL).

**Compiler/runtime requirements:** Karkain v1.2.0; the VERIFIED Windows x86-64 C-free backend with the Go front-end engine is the only currently working compile/run path on this host.

**stdlib modules actually present (per capability register):** `net` PRESENT (5 s read timeout documented); `http` PRESENT (18 functions, building blocks only, no concurrent server framework); `db` PRESENT/PARTIAL (SQL subset + transactions); `crypto` PRESENT; `encoding` PRESENT; `json` PARTIAL; `system`/`env`/`process`/`time` PARTIAL shell wrappers (MUST be probed before any timing claim); `async` PRESENT but cooperative, not preemptive.

**Required ecosystem components:** None external for MVP; Zabbix/PRTG may consume JSON output externally but are not runtime dependencies.

**Missing dependencies:** UDP/datagram, TLS, WebSocket, concurrent poller, OS metric/process APIs, full JSON DOM — all absent.

**Native implementation opportunities:** Karkain-native sequential scheduler, threshold comparator and text/JSON renderer using `db2`/`string`/`collections`.

**FFI/OS integration needs:** None justified for MVP. Any future OS integration must be a Karkain-compatible native interface, never another language.

**Build/packaging:** Single Karkain binary plus text config tree; `karkain build`/`karkain run` with the C-free target.

**Explicit blockers (each a BLOCKER, not solved):** no UDP (SNMP/ICMP packet loss, NetFlow/sFlow traffic, syslog all impossible); no concurrent poller (sequential only); no TLS; no WebSocket (no push events); no GUI; no host OS metrics (PARTIAL wrappers). No workaround may change Karkain or introduce another language.

## G. Non-functional requirements

| Target | Workload + measurement method |
|---|---|
| Performance/latency | Run 50 active TCP/HTTP checks in one pass; measure wall-clock via a Karkain-native timing harness (PARTIAL `time` — probe first). |
| Memory | Retain 10,000 history points; measure process RSS with an external OS tool. |
| CPU | Run the 50-check pass; measure elapsed CPU via `karkain prof` where applicable. |
| Scalability | 100 sequential checks; confirm pass duration and memory stay bounded. |
| Reliability | Simulate unreachable target; confirm failed check event, no crash, prior history intact. |
| Security | Reject malformed targets and oversized input; TLS absent — transport security NOT provided. |
| Privacy | Events contain target identifiers only; no credentials in exported events. |
| Portability | Build with C-free Windows target; cross-compile for Linux and record execution NOT VERIFIED. |
| Maintainability | Each module lint-clean with documented interfaces. |
| Accessibility | All output text/JSON; no GUI dependency. |
| Observability | Every check pass and threshold change emits a structured event. |
| Recovery | Restore config + `db2` dump into empty workspace and revalidate. |

Practical machine constraint: ~4 GB RAM; workloads sized accordingly.

## H. Compatibility and portability

**Supported OS:** Windows x86-64 toolchain VERIFIED; Linux cross-target output PRESENT but execution NOT VERIFIED; macOS NOT VERIFIED. **Architectures:** x86-64 for the VERIFIED target; others unverified. **Karkain v1.2.0 compatibility matrix:** v1.2.0 only. **Binary/package formats:** single native executable plus text config tree. **Install/uninstall:** copy/unpack binary and config; uninstall by removal. **Config migration:** schema version field planned; no pre-v1.2.0 compatibility required. **Data migration:** documented text/JSON export/import. **Backward compatibility:** none required before v1.2.0. **Upgrade/rollback:** keep prior binary/config; rollback by restore + revalidate. **Cross-platform differences:** `time`/`system` shell-outs vary (PARTIAL/UNVERIFIED). **Unsupported:** UDP/SNMP/ICMP, flow ingest, TLS, WebSocket, concurrent polling, GUI, host OS metrics.

## I. Testing strategy

All tests are **PLANNED**; none have been run for this application.

- **Unit:** Threshold comparator, scheduler, event renderer; criterion — known inputs yield exact outputs.
- **Integration:** Define checks → run pass → breach threshold → emit event → store history; criterion — one event per breach, no state corruption.
- **E2E:** Load check config, run pass, verify text/JSON fixtures.
- **Platform-specific:** C-free Windows build executes (exit 0); Linux/macOS execution NOT VERIFIED; PARTIAL `time` probed before timing claims.
- **Security:** malformed target/oversized input rejected; criterion — no unintended network/file access.
- **Performance:** 50-check pass and 10,000-point history within G workloads; bounded memory.
- **Regression:** prior fixes remain correct.
- **Compatibility:** lint/build on v1.2.0; zero errors.
- **Actual-compiled-executable:** build with `karkain build`, run binary, exit 0 and expected output.
- **Failure/recovery:** unreachable target and failed write; clean failure, prior history intact.

## J. Implementation roadmap

**MVP:** Check registry, config loader, single sequential pass, text output; gate — first vertical slice compiles and runs.

**Core functional release:** Health sampler, threshold engine, alert emitter, history store; gate — unit/integration/E2E criteria pass.

**Advanced release:** JSON event integration, retention, richer text dashboard; gate — security/recovery criteria pass.

**Production-readiness release:** hardening and platform probes; gate — all G targets measured and BLOCKED capabilities either resolved at language level or still explicitly documented.

**Feature dependencies:** Health sampler depends on Check Registry; Threshold Engine depends on results; Alert Emitter depends on thresholds; History depends on results; SNMP/flow/push/concurrency depend on future language capabilities.

**Milestones/gates:** each release proceeds only after its gate; no fictional dates or completion claims. **Risks/mitigations:** BLOCKED primitives kept as blockers (never faked); PARTIAL modules probed before reliance; single-threaded design documented as a constraint.
