# AI-server-plugin

> Bring modern health monitoring to older Dell PowerEdge + VMware ESXi hardware — plus an AI agent that researches, retrieves, and provisions new services on its own.

![Status](https://img.shields.io/badge/status-legacy%20%7C%20cleanup%20in%20progress-orange)
![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![Platform](https://img.shields.io/badge/platform-VMware%20ESXi%206.7-lightgrey)
![Hardware](https://img.shields.io/badge/tested%20on-Dell%20PowerEdge%20R720-darkblue)
![License](https://img.shields.io/badge/license-MIT-green)

---

## Overview

**AI-server-plugin** is a monitoring and management tool for **Dell PowerEdge servers
running legacy VMware ESXi (6.7)**. It backports the kind of host and hardware health
monitoring found in newer ESXi releases onto older enterprise gear that would otherwise
be left behind by modern tooling.

It was built and validated in a homelab on a **Dell PowerEdge R720**, and includes an
experimental **AI agent layer** that can research, retrieve, and provision new
applications and services with minimal hand-holding.

> ⚠️ **Legacy notice:** This project is deprecated and preserved for reference. It is
> currently being cleaned up and refactored. It works, but expect rough edges — see
> [Roadmap](#roadmap).

## Why it exists

Most real-world infrastructure is **not** on the newest release. Enterprises routinely
run hardware and hypervisors a generation or two behind, where vendor tooling has moved
on and support gets thin. This project exists to serve *that* environment — the one
actual users live in — rather than the demo one.

## Features

- 🩺 **Host & VM health** — CPU, memory, datastore usage, VM power states, and uptime,
  pulled live from the vSphere API.
- 🌡️ **Hardware sensors** — fan speeds, temperatures, and PSU status read from the Dell
  **iDRAC7 / IPMI** interface, so you see the physical box, not just the hypervisor.
- 🚨 **Threshold alerting** — configurable warning/critical thresholds with a clean
  status rollup.
- 🤖 **AI provisioning agent** *(experimental)* — give it a goal (e.g. "stand up a
  Pi-hole"), and it researches the steps, fetches what it needs, and provisions the
  service.
- 📊 **Lightweight dashboard** — a simple local web view summarizing host, VM, and
  hardware state at a glance.

## Screenshots

> Drop real screenshots into `docs/img/` and they'll render here.

| Dashboard | Hardware sensors |
| --- | --- |
| ![Dashboard](docs/img/dashboard.png) | ![Sensors](docs/img/sensors.png) |

## Architecture

```text
                 ┌──────────────────────────┐
                 │         You / CLI         │
                 └────────────┬─────────────┘
                              │
                 ┌────────────▼─────────────┐
                 │   AI-server-plugin core   │
                 │  (scheduler + dashboard)  │
                 └───┬───────────┬───────┬───┘
                     │           │       │
        ┌────────────▼──┐  ┌─────▼────┐  ┌▼──────────────┐
        │  vSphere API  │  │  IPMI /  │  │   AI Agent    │
        │  (pyVmomi)    │  │  iDRAC7  │  │   (LLM)       │
        │  ESXi 6.7     │  │  sensors │  │   research +  │
        │  hosts / VMs  │  │  temp/fan│  │   provision   │
        └───────────────┘  └──────────┘  └───────────────┘
```

## Tech stack

`Python 3.10+` · `pyVmomi` (vSphere API) · `IPMI / iDRAC7` · `Flask` (dashboard) ·
`schedule` · `rich` · LLM API for the agent layer

## Requirements

- A Dell PowerEdge host (built/tested on **R720**) running **VMware ESXi 6.7**
- **iDRAC / IPMI** enabled for hardware sensor reads
- Python **3.10+** on the machine running the tool
- An LLM API key if you want the AI agent features

## Installation

```bash
git clone https://github.com/StefanYasin/AI-server-plugin.git
cd AI-server-plugin
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## Configuration

Copy the example environment file and fill in your values:

```bash
cp .env.example .env
```

Never commit `.env` — it's already in `.gitignore`.

## Usage

```bash
# One-off health check to the console
python -m ai_server_plugin status

# Launch the local dashboard
python -m ai_server_plugin dashboard

# Run the AI provisioning agent with a goal
python -m ai_server_plugin agent --goal "set up a Pi-hole DNS sinkhole"
```

## Roadmap

- [ ] Refactor core modules and remove dead code (cleanup pass)
- [ ] Add unit tests around the vSphere and IPMI collectors
- [ ] Harden the AI agent with a dry-run / confirmation mode
- [ ] Container image for one-command deployment
- [ ] Migrate optional Redfish support for newer iDRAC generations

## Disclaimer

Provided as-is for homelab and educational use. Interacting with server hardware,
hypervisors, and an autonomous provisioning agent carries risk — **test in a
non-production environment**. You are responsible for any changes it makes.

## License

[MIT](LICENSE) © 2026 Stefan Yasin

## Author

**Stefan Yasin** — Technical Customer Success · 30+ year technologist
[LinkedIn](https://www.linkedin.com/in/stefanyasin/) · [GitHub](https://github.com/StefanYasin)
