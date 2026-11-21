# Karkain Network Monitor — First Implementation Backlog

## First vertical slice

**Slice goal:** Load two declarative checks (one TCP-connect, one HTTP GET), run a single sequential pass and print each target with a reachability result. This is real work — config parsing, TCP/HTTP probing and text rendering — without depending on any BLOCKED capability.

**Karkain modules/files expected:** `src/main.kark` (command dispatch), `src/config/loader.kark` (config read/validate), `src/checks/registry.kark` (check records), `src/checks/sampler.kark` (TCP/HTTP probe via `net`/`http`), `src/ui/text_output.kark`, `configs/default.kark`, `examples/checks.kark` (sample checks).

**Exact input:** `examples/checks.kark` declaring `id = "tcp-local"`, `type = "tcp"`, `target = "127.0.0.1:9000"`, `timeout_ms = 5000` and `id = "http-local"`, `type = "http"`, `target = "http://127.0.0.1:9000/health"`, `timeout_ms = 5000`. Command: `karkain run src/main.kark checks run examples/checks.kark`.

**Exact expected observable output:** one line per check containing id, target and `up` or `down` (e.g. `tcp-local 127.0.0.1:9000 down`), followed by `checks: 2`. Latency is shown only if a verified timing probe exists; otherwise the field is `latency: unknown` — never a fabricated value. An invalid check definition is reported with its field and no probe is attempted.

**Verification command pattern (Karkain v1.2.0):** with the VERIFIED Go front-end engine and C-free Windows target selected, run `karkain lint src/main.kark`, then `karkain build src/main.kark -o net-mon-slice` (or `karkain run src/main.kark ...`), execute the binary and compare stdout to the expected fixture. Commands become active only when implementation starts.

## Backlog items

| ID | Feature | Files/modules | Dependencies | Acceptance criteria | Tests | Complexity | Blocking Karkain capability | Definition of done |
|---|---|---|---|---|---|---|---|---|
| MON-001 | Config loader + check validation | `src/config/loader.kark` | config, io | Valid checks load; invalid field reported with position | Unit | M | None | Lint-clean; fixture passes |
| MON-002 | Check registry | `src/checks/registry.kark` | MON-001 | Duplicate ids rejected; type/target validated | Unit, integration | M | None | Registry fixture passes |
| MON-003 | TCP-connect sampler | `src/checks/sampler.kark` | MON-002, net | Reachable→up, unreachable→down after documented timeout | Unit, failure/recovery | M | UDP/ICMP BLOCKED — TCP only; TLS BLOCKED | Sampler fixture passes |
| MON-004 | HTTP GET sampler | `src/checks/http_sampler.kark` | MON-002, http | Status code captured; failure yields structured result | Integration | M | Concurrent server framework BLOCKED — client only | HTTP fixture passes |
| MON-005 | Single sequential poll pass | `src/poller/scheduler.kark` | MON-003, MON-004 | All checks run in order; bounded duration | Integration, performance | M | Concurrent poller BLOCKED | Pass fixture passes |
| MON-006 | Text/JSON output | `src/ui/text_output.kark`, `src/ui/json_output.kark` | MON-005 | Identical data in both modes; deterministic order | Unit, E2E | S | json PARTIAL for JSON mode | Output fixtures pass |
| MON-007 | Timing probe + unknown fallback | `src/checks/timing.kark` | MON-005, time PARTIAL | Probe attempted first; failure records `unknown`, never fabricated | Platform-specific, security | M | time PARTIAL — must be probed; no verified latency otherwise | Probe report written |
| MON-008 | Threshold engine | `src/thresholds/engine.kark` | MON-007, db2 | Per-check threshold and severity evaluated; hysteresis works | Unit, integration | L | None (string/db2 PRESENT) | Threshold fixture passes |
| MON-009 | Alert event emitter | `src/alerts/emitter.kark` | MON-008, io | Append-only event per breach; stdout event matches | Integration | M | WebSocket BLOCKED — no push | Event fixture passes |
| MON-010 | History store + retention | `src/history/store.kark` | MON-005, db2 | Results stored; retention trims old points; rollback on failure | Regression, failure/recovery | M | Crash-atomicity UNVERIFIED | History round-trip passes |
| MON-011 | JSON event export for Zabbix/PRTG | `src/integrations/export.kark` | MON-006, MON-009 | JSON events parse with simple field extraction | E2E, compatibility | M | json PARTIAL — no full DOM; syslog/UDP BLOCKED | Export fixture passes |
| MON-012 | Native slice verification harness | `tests/slice_verification.kark`, `scripts/verify.ps1` | MON-005, MON-006 | Build produces executable; stdout matches fixture; exit 0 | Actual-compiled-executable | M | Default kcc engine BLOCKED — use Go engine + C-free target | Harness passes end-to-end |

## Dependency order and gates

1. MON-001 → gate: loader fixture passes. 2. MON-002 → gate: registry fixture passes. 3. MON-003, MON-004 → MON-005. 4. MON-006 → MON-007 (probe before any timing claim). 5. MON-008 → MON-009. 6. MON-010, MON-011. 7. MON-012. No item proceeds until its gate passes and dependencies are lint-clean.

## Status

All items **NOT STARTED**. Implementation must not begin until all designs and repository initialization are complete. No tests have been run for this application; no application binary exists; all BLOCKED capabilities (UDP/SNMP/ICMP, flow ingest, TLS, WebSocket, concurrent poller, GUI, host OS metrics) remain blockers and must not be claimed as solved.
