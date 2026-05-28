# SCCM Lab: Meridian Freight Co.

> **Status: Work in Progress.** This repo is being built incrementally. Scenario, architecture, and build runbook docs are in place. PowerShell scripts, diagrams, and screenshots land phase by phase as the lab is rebuilt for evidence capture.

> A home lab built to simulate a real SMB rollout of Microsoft Configuration Manager (SCCM / MECM) and WSUS for a fictional 120-user freight company across two sites.

This repo documents the scenario, architecture, build runbook, and deployment outcomes. It is a lab, but every decision (naming, OUs, GPOs, patching, OSD, application deployment) is shaped by how an actual IT engineer would run it in production for a mid-market company.

---

## The fictional company

**Meridian Freight Co.**: regional logistics provider, 120 employees, two sites (Headquarters and a Warehouse satellite). The lab simulates the workload of a one-engineer IT shop standing up endpoint management, patching, OS deployment, and application delivery from scratch.

See [docs/01-scenario.md](docs/01-scenario.md) for the full business story and drivers.

## Stack

* **Hypervisor:** Hyper-V on a Windows 11 Pro host
* **OS:** Windows Server 2022 (infrastructure), Windows 11 (clients)
* **Identity:** Active Directory Domain Services, DNS
* **Database:** SQL Server 2022 (Standard)
* **Endpoint management:** Microsoft Configuration Manager (current branch)
* **Patching:** WSUS as the SCCM Software Update Point
* **Print:** Windows Print and Document Services
* **Automation:** PowerShell (idempotent, parameterized)

## What the lab covers

* AD forest build, OU structure, group policy (security baselines, drive mapping, printer mapping)
* SQL Server prerequisites for SCCM (collation, SPNs, service accounts)
* SCCM primary site install, boundary groups, client push, role distribution
* WSUS / SUP configuration, ADR (Automatic Deployment Rules) for monthly patching
* Windows 11 OS deployment via PXE (custom thin image)
* Application packaging and deployment (Microsoft 365, Google Chrome, PDF reader)
* Printer deployment via SCCM and GPO comparison
* RBAC inside SCCM (helpdesk role, server admin role)

## Repo map

```
.
├── README.md                  This file
├── docs/                      Scenario, architecture, runbook, deployments, lessons
├── diagrams/                  Topology and AD structure diagrams
├── screenshots/               Build evidence, console captures
├── scripts/                   PowerShell automation (idempotent, parameterized)
├── gpo-exports/               Backed-up GPOs as XML
├── LICENSE                    MIT
└── .gitignore
```

## Author

Ajay Angdembe. Built as a portfolio piece to demonstrate end-to-end design and operation of Microsoft endpoint management for a realistic small-business environment.

## License

MIT. See [LICENSE](LICENSE).
