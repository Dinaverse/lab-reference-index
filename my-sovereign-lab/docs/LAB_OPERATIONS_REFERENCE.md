# 🛠️ Laboratory Operations & Security Reference

This document serves as the technical reference for the operations, maintenance, and security protocols of the laboratory infrastructure.

## 1. Introduction
This document defines the methodology for managing the laboratory infrastructure, ensuring it remains sovereign, automated, and secure. All modifications must adhere to these established principles.

## 2. Logical Topology (Nodes & Roles)
Communication across the lab relies exclusively on the encrypted Tailscale tunnel.

| Host Name | Role | Tailscale IP | SSH Method |
| :--- | :--- | :--- | :--- |
| Kali Station | Orchestrator/Controller | <KALI_TAILSCALE_IP> | SSH CA/Key |
| Arch Cluster | Compute IA (4x P106-100) | <ARCH_CLUSTER_TAILSCALE_IP> | SSH CA/Key |
| Raspberry Pi | Pi-hole/Services | <RASPBERRY_PI_TAILSCALE_IP> | SSH CA/Key |
| Dell Precision | Monitoring/Orchestrator | N/A | SSH CA/Key |
| Canwork189 | Storage/General | <CANWORK189_TAILSCALE_IP> | SSH CA/Key |
| Canwork164 | Storage/General | <CANWORK164_TAILSCALE_IP> | SSH CA/Key |

## 3. Security Posture (Hardening)
- **Transport:** Encrypted tunnel (Tailscale) is mandatory for all remote access.
- **Firewall:** SSH access restricted to Tailscale interfaces via `nftables`/`iptables`.
- **Authentication:**
    - Routine password authentication is disabled.
    - SSH CA is enabled; connections rely on certificates signed by the lab's master Certificate Authority.
- **Intrusion Protection:** Fail2Ban configured on control stations.

## 4. Orchestration & Automation
Laboratory management is powered by custom skills integrated with Gemini CLI:
- **Auditing:** `lab-auditor` (Material/Software state).
- **Control:** `agent-controller` (Systemd service management).
- **Deployment:** `deploy-docker-stack` (Docker/Compose orchestration).
- **Archiving:** `log-archiver` (Automated log backup).

## 5. Maintenance Procedures
- **Adding a Node:**
    1. Install Tailscale.
    2. Configure SSH CA trust (`TrustedUserCAKeys`).
    3. Harden firewall rules.
- **Certificate Renewal:** Periodically sign keys using the master CA.
- **Agent Persistence:** Utilize `systemd` user-level services with `linger` enabled for resilience.

## 6. Access Control
- **Authentication:** Certificat SSH signé (valide 1 an).
- **CA Private Key (`lab_ca`):** To be stored on ultra-secure storage (must never leave Kali).
- **User Identity:** `dina` (or `newdina` on Canwork).

## 7. Daily Usage (SSH Connections)
With the SSH CA and `~/.ssh/config` configured, access is simplified:
- Use the command: `ssh <user>@<tailscale_node_name>`
- The SSH client automatically presents the required signed certificate.
- No further manipulation is required.

## 8. TROUBLESHOOTING & DÉPANNAGE
- **Problème :** Blocage de `do-release-upgrade` par des PPA tiers et fichiers sources corrompus.
  - **Résolution :** Suppression manuelle des fichiers PPA conflictuels dans `/etc/apt/sources.list.d/`, restauration de `sources.list` par défaut, et exécution de `do-release-upgrade`.
- **Problème :** Incompatibilité Ansible avec Python 3.8 (sur Ubuntu 20.04).
  - **Résolution :** Mise à niveau de l'OS vers Ubuntu 22.04+ pour bénéficier de Python 3.10+ natif.

---
*Technical Reference Version 1.1 (2026-06-14)*
