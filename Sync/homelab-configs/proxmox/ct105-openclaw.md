================================================================
 OPENCLAW SETUP — PROXMOX CT105
 Date: 2026-07-05
 Author: Dina Cima Mufungizi (dianacima852@gmail.com)
================================================================

OVERVIEW
--------
OpenClaw is a self-hosted, always-on AI agent gateway running
on Proxmox LXC container CT105 (hostname: openclaw). It uses
the Qwen 3.5 27B model served locally by Ollama on the Arch
Linux machine — no cloud API key required.

INFRASTRUCTURE
--------------
  Proxmox Host  : root@100.71.115.27     (dina, Tailscale)
  CT105          : root@100.127.120.1    (openclaw, Tailscale)
  Ollama (Arch)  : dina@100.69.65.101   (archlinux, Tailscale)
  Ollama port    : 11434 (bound to 100.69.65.101 via Tailscale)

CT105 SPECS
-----------
  ID             : 105
  Hostname       : openclaw
  OS             : Ubuntu 24.04 LTS
  CPU            : 2 vCPU
  RAM            : 2048 MB
  Swap           : 512 MB
  Disk           : 20 GB (local-lvm)
  Features       : nesting=1
  Onboot         : yes
  Tailscale IP   : 100.127.120.1

LXC EXTRA CONFIG (/etc/pve/lxc/105.conf)
-----------------------------------------
  lxc.cgroup2.devices.allow: c 10:200 rwm
  lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
  (Required for Tailscale TUN device access inside the container)

INSTALLED SOFTWARE
------------------
  - Node.js 22 (via NodeSource apt repo)
  - OpenClaw 2026.6.11 (npm install -g openclaw)
  - Tailscale 1.98.8

OLLAMA SETUP (Arch Linux — 100.69.65.101)
------------------------------------------
  Model installed : qwen3.5:27b (27.8B, Q4_K_M, 17 GB)
  GPU backend     : Vulkan (OLLAMA_VULKAN=1)

  Override file   : /etc/systemd/system/ollama.service.d/tailscale.conf
  Contents:
    [Service]
    Environment=OLLAMA_HOST=100.69.65.101:11434

  This binds Ollama to the Tailscale interface so CT105 can reach
  it. Previously it was locked to 127.0.0.1 only.

  To apply changes:
    sudo systemctl daemon-reload && sudo systemctl restart ollama

OPENCLAW CONFIGURATION (on CT105)
-----------------------------------
  Config file  : /root/.openclaw/openclaw.json
  Provider     : archollama (custom Ollama provider)
  Base URL     : http://100.69.65.101:11434
  API adapter  : ollama (native)
  Default model: archollama/qwen3.5:27b
  Gateway port : 18789 (localhost, accessible within CT105)
  Gateway auth : persistent token mode

SYSTEMD SERVICE (CT105)
------------------------
  File: /etc/systemd/system/openclaw.service

  [Unit]
  Description=OpenClaw AI Gateway
  After=network-online.target tailscaled.service
  Wants=network-online.target

  [Service]
  Type=simple
  User=root
  EnvironmentFile=/root/.openclaw.env
  ExecStart=/usr/bin/openclaw gateway run --force
  Restart=always
  RestartSec=10

  [Install]
  WantedBy=multi-user.target

  Status commands:
    systemctl status openclaw
    journalctl -u openclaw -f
    systemctl restart openclaw

PERSISTENCE CHECKLIST
---------------------
  [x] CT105 onboot=1 in Proxmox
  [x] openclaw.service enabled
  [x] tailscaled.service enabled
  [x] Ollama service enabled on Arch
  [x] Ollama Tailscale override persists via drop-in config
  [x] Gateway auth token persisted in openclaw.json

USEFUL COMMANDS
---------------
  # SSH into CT105
  ssh root@100.127.120.1

  # Check OpenClaw status
  pct exec 105 -- systemctl status openclaw
  pct exec 105 -- journalctl -u openclaw -n 30 --no-pager

  # Check Ollama reachable from CT105
  pct exec 105 -- curl -s http://100.69.65.101:11434/api/tags

  # List agents
  pct exec 105 -- openclaw agents list

  # Check model status
  pct exec 105 -- openclaw models status

  # Check Ollama models on Arch
  ssh dina@100.69.65.101 ollama list

ADDING MORE OLLAMA MODELS
--------------------------
  SSH into Arch:
    ssh dina@100.69.65.101
    ollama pull <model-name>

  Then patch OpenClaw config on CT105 and restart:
    openclaw config patch --file /tmp/oc-patch.json
    openclaw models set archollama/<model-name>
    systemctl restart openclaw

ADDING CHAT CHANNELS (Telegram, Discord, etc.)
-----------------------------------------------
  OpenClaw supports messaging channels so you can chat with
  the agent from your phone. To add one:
    pct exec 105 -- openclaw channels add

================================================================
 END OF DOCUMENT
================================================================
