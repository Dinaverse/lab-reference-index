# 🧪 My Sovereign Lab — Infrastructure Reference

> **NOTICE:** This repository is a reference hub. For the complete professional overview of all projects and portfolio, visit **[Dinaverse](https://github.com/Dinaverse/Dinaverse)** — the master documentation.

---

## 🎯 Purpose

This repository exists as a **detailed technical reference** for the sovereign laboratory infrastructure. It complements the main documentation with:

- Node specifications and hardware details
- Agent deployment configurations
- Security procedures and hardening steps
- Historical reports and operational logs
- Advanced troubleshooting procedures

---

## ⚠️ For Complete Overview, Start Here

| Document | Purpose |
|----------|---------|
| **[Dinaverse Master README](https://github.com/Dinaverse/Dinaverse)** | Complete portfolio and project map |
| **[sovereign-ai-infrastructure](https://github.com/Dinaverse/sovereign-ai-infrastructure)** | Central architecture documentation |
| **[local-ai-sovereign-stack](https://github.com/Dinaverse/local-ai-sovereign-stack)** | Docker AI stack deployment |

---

## 🖥️ Node Specifications

### Kali Station (Master Orchestrator)
- **CPU:** Intel Xeon E5-2630 v4 @ 2.20GHz
- **RAM:** 62 GiB
- **Storage:** 909 GiB total
- **Role:** Master orchestration, SecOps hub, MCP bridge
- **Status:** ✅ Online

### Arch Cluster (GPU Compute)
- **CPU:** Intel Core i5-6500 @ 3.20GHz
- **RAM:** 15 GiB
- **Storage:** 119 GiB total
- **GPUs:** 4× NVIDIA P106-100 (24 GB VRAM total)
- **Role:** LLM inference, multi-GPU compute
- **Status:** ✅ Online

### Raspberry Pi (Network Services)
- **CPU:** Cortex-A53
- **RAM:** ~1 GiB
- **Storage:** 29 GiB total
- **Role:** IDS (Suricata), DNS, network monitoring
- **Status:** ✅ Online

### Dell Server (Gateway & Monitoring)
- **CPU:** Dell enterprise processor
- **RAM:** 16+ GiB
- **Storage:** 1TB+
- **Role:** Internet gateway, Prometheus/Grafana
- **Status:** ✅ Online

### AMD Canwork189 (Storage & CPU)
- **CPU:** AMD FX-8320 Eight-Core Processor
- **RAM:** 7.2 GiB
- **Storage:** 46 GiB root + 159 GiB /local
- **Role:** Distributed storage, CPU-bound workloads
- **Status:** ✅ Online

---

## 🔧 Agent Deployment (Arch Cluster)

Agents are deployed in `~/agents/` and managed as systemd services.

| Agent | Function | Execution | Status |
|-------|----------|-----------|--------|
| **Security-Ops** | Log monitoring & analysis | Continuous daemon | ✅ Running |
| **Net-Analyzer** | Network reconnaissance | Hourly schedule | ✅ Active |
| **R&D Agent** | Training & experimentation | Manual trigger | ✅ Ready |

### Agent Commands

```bash
# Check agent status
systemctl status security-ops-agent
systemctl status net-analyzer-agent

# View logs
journalctl -u security-ops-agent -f
journalctl -u net-analyzer-agent -f

# Restart agent
systemctl restart security-ops-agent
```

---

## 🛡️ Security & Hardening

### SSH Configuration

All nodes use key-based authentication with passwordless access:

```bash
# SSH key for lab: ~/.ssh/id_lab
chmod 600 ~/.ssh/id_lab

# SSH config for quick access
Host arch-gpu
  HostName <ARCH_CLUSTER_IP>
  User dina
  IdentityFile ~/.ssh/id_lab
  Port 22
```

### Firewall Rules

- **Incoming:** Only SSH (22), Prometheus (9090 - internal), Ollama (11434 - internal)
- **Outgoing:** DNS (53), NTP (123), HTTP/HTTPS (80/443 for updates)
- **Internal:** Unrestricted inter-node communication

### Hardening Procedures

1. **SSH hardening** — Disabled root login, password auth, empty passwords
2. **Firewall rules** — UFW / iptables configured per node
3. **Log monitoring** — Suricata IDS active on Raspberry Pi
4. **Network segmentation** — VLAN isolation for different workloads

---

## 📊 Monitoring & Observability

### Prometheus Scrape Targets

| Target | Port | Interval |
|--------|------|----------|
| Arch-GPU (node metrics) | 9100 | 15s |
| Arch-GPU (Ollama) | 11434 | 15s |
| Dell-Gateway (node metrics) | 9100 | 15s |
| Raspberry-Pi (Suricata) | 9114 | 15s |

### Grafana Dashboards

- **AI Metrics** — LLM inference performance
- **GPU Performance** — VRAM, compute efficiency
- **System Health** — CPU, memory, disk across all nodes
- **Security Events** — IDS alerts, failed logins
- **Service Status** — Docker container health

---

## 🔄 Persistence & Automation

### Systemd Services

All critical services are registered as systemd units:

```bash
# List lab services
systemctl list-units --type=service | grep lab

# Enable service on reboot
systemctl enable ollama
systemctl enable security-ops-agent

# Manual service restart
systemctl restart ollama
```

### Docker Container Management

```bash
# View running containers
docker-compose ps

# View logs
docker-compose logs -f ollama

# Restart container
docker-compose restart ollama
```

---

## 📁 Directory Structure

```
my-sovereign-lab/
├── README.md                          (this file)
├── docs/
│   ├── LAB_COMPREHENSIVE_FINAL_REPORT.md
│   ├── LAB_OPERATIONS_REFERENCE.md
│   ├── SYNCTHING_TROUBLESHOOTING.md
│   └── SECURITY_PROCEDURES.md
├── hardware/
│   └── HARDWARE_BOM.md
├── configuration/
│   ├── ssh-config.example
│   ├── firewall-rules.sh
│   └── systemd-units/
└── monitoring/
    ├── prometheus-targets.yaml
    └── grafana-dashboards/
```

---

## 📖 Related Documentation

| Document | Purpose |
|----------|---------|
| **[SSH & Network Security](docs/SECURITY_PROCEDURES.md)** | Hardening & access control |
| **[Operations Reference](docs/LAB_OPERATIONS_REFERENCE.md)** | Daily operations procedures |
| **[Syncthing Troubleshooting](docs/SYNCTHING_TROUBLESHOOTING.md)** | Remote access issues |
| **[Comprehensive Final Report](docs/LAB_COMPREHENSIVE_FINAL_REPORT.md)** | Historical status & migration |

---

## 🔗 Related Repositories

| Repository | Purpose |
|------------|---------|
| **[Dinaverse](https://github.com/Dinaverse/Dinaverse)** | Master README & project overview |
| **[sovereign-ai-infrastructure](https://github.com/Dinaverse/sovereign-ai-infrastructure)** | Architecture documentation |
| **[local-ai-sovereign-stack](https://github.com/Dinaverse/local-ai-sovereign-stack)** | Docker stack |
| **[cybersecurity-lab-automation](https://github.com/Dinaverse/cybersecurity-lab-automation)** | Security automation |

---

## ✅ Lab Status (2026-07-05)

| Component | Status |
|-----------|--------|
| Multi-GPU LLM Inference | ✅ Operational |
| Grafana / Prometheus | ✅ Operational |
| Suricata IDS | ✅ Operational |
| Docker Services | ✅ Operational |
| n8n Workflows | ✅ Operational |
| Security Agents | ✅ Running |
| All Nodes | ✅ Online |

---

*Infrastructure is constantly evolving. See main Dinaverse repository for latest portfolio.*
