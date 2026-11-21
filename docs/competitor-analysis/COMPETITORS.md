# Competitor Analysis - Karkain Network Monitor

Source: consolidated portfolio research _portfolio-foundation/research/domain4-networking.md, section for Karkain Network Monitor. All changing facts accessed 2026-11-21. Evidence labels: VERIFIED FACT / RESEARCH-BASED INTERPRETATION / PROPOSAL / UNVERIFIED ASSUMPTION. Competitor capabilities and prices are reproduced as researched, never invented.

## 4. Karkain Network Monitor â€” Zabbix, PRTG (+ LibreNMS-style FOSS)

**Positioning/users. [VF]** Zabbix is free open-source observability for infrastructure, apps and services â€” metrics, logs, traces, events â€” with correlation, alerting and remediation. PRTG is Paessler's all-in-one IT/OT/IoT monitor built on "sensors" (one measured value; 5â€“10 per device). **[RI]** Template/API-driven OSS versus fast-start sensor-licensed.

**Capabilities. [VF]** Zabbix: SNMP poll/trap, IPMI, JMX, VMware, C/Go agents, flexible triggers, macro escalations, graphs/maps, discovery, inheritable templates, web monitoring, business-service trees, SLA calculation, event streaming to Kafka/Kinesis. PRTG: 250+ sensors, custom PowerShell/Python/REST/SSH/HTTP-push sensors, maps, alerts, reports, probes, failover cluster.

**Platform/deployment. [VF]** Zabbix server on Linux/Unix, agents on Linux/Windows, PHP web UI, cloud nodes or AWS/Azure/GCP images. PRTG core server is Windows-only with Linux probes; on-prem or Hosted; 30-day trial (10-day hosted).

**Architecture/security. [VF]** Zabbix: server, DB, web UI, proxies, agents, API, custom widgets/modules; component and DB encryption, RBAC, tokens, audit logs, HashiCorp/CyberArk vaults. PRTG: core plus probes, SNMP v1/v2c/v3, WMI, SSH, packet sniffing, NetFlow/jFlow/sFlow/IPFIX, granular access rights.

**Scalability. [VF]** Zabbix scales via proxy load balancing; Cloud NVPS tiers 50 (Nano) to 10,000 (2xLarge). PRTG licenses 500â€“10,000 sensors (â‰ˆ50â€“1,000 devices); above 1k use PRTG Enterprise Monitor.

**Pricing (official only). [VF]** Zabbix on-prem is free under AGPL-3.0; subscriptions $325/mo Silver, from $825 Gold, higher tiers custom, unlimited devices/metrics/users. Cloud from $50/mo. PRTG: 500 sensors $200/mo, 1,000 $358, 2,500 $742, 10,000 $1,642 annually, net VAT; freeware 100 sensors.

**Strengths/weaknesses. [VF]** Zabbix has no per-device limits and deep customisation but needs a DB/web stack and manual partitioning. PRTG has ready sensors and easy discovery but sensor licensing raises cost and the core is Windows-only.

**Gaps. [RI]** Constrained/edge sites need a text-native monitor: no DB, no GUI, no per-sensor fees.

**Differentiation. [PROP]** CLI/TUI/service-first monitor with declarative checks, local thresholds and text/JSON events over the existing TCP/HTTP blocks. **[BLOCKED]** no UDP (SNMP/ICMP, NetFlow/sFlow, syslog), no TLS, no concurrent poller, no GUI. Position as a composable probe feeding Zabbix/PRTG.

| Karkain proposed capability | Zabbix | PRTG |
|---|---|---|
| CLI/TUI-first; GUI **[BLOCKED]** | PHP web UI + API | Web/desktop/mobile UIs |
| No database dependency **[PROP]** | Requires DB | Requires core server |
| Active TCP/HTTP checks | Yes | Yes (250+ sensors) |
| UDP/SNMP/flow ingest **[BLOCKED]** | Yes | Yes |
| TLS transport **[BLOCKED]** | Yes | Yes |
| Concurrent multi-target polling **[BLOCKED]** | Yes (proxies) | Yes (probes) |
| No per-sensor licensing **[PROP]** | Free OSS + support | Sensor licensing |

**Sources (all accessed 2026-11-21):** https://www.zabbix.com/features Â· https://www.zabbix.com/documentation/current/en/manual/introduction/overview Â· https://www.zabbix.com/capabilities Â· https://www.zabbix.com/subscriptions Â· https://www.zabbix.com/cloud Â· https://www.paessler.com/prtg/prtg-network-monitor Â· https://www.paessler.com/pricing Â· https://www.paessler.com/prtg/features/sensors Â· https://www.paessler.com/manuals/prtg/available_licenses

---

