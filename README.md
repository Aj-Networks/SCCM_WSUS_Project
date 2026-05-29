<div align="center">

# SCCM Lab: Meridian Freight Co.

**A home lab that simulates a real SMB rollout of Microsoft Configuration Manager (SCCM / MECM) and WSUS for a fictional 120-user freight company across two sites.**

[![Status](https://img.shields.io/badge/status-Work%20in%20Progress-yellow?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](LICENSE)
[![Type](https://img.shields.io/badge/Type-Home%20Lab-success?style=for-the-badge)](#)
[![Platform](https://img.shields.io/badge/Platform-Hyper--V-0078D6?style=for-the-badge&logo=microsoft&logoColor=white)](#)
[![PowerShell](https://img.shields.io/badge/PowerShell-7-5391FE?style=for-the-badge&logo=powershell&logoColor=white)](#)
[![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-0078D6?style=for-the-badge&logo=windows&logoColor=white)](#)

</div>

---

> [!NOTE]
> **This repo is a work in progress.** Scenario, architecture, and build runbook docs are in place. PowerShell scripts, diagrams, and screenshots land phase by phase as the lab is rebuilt for evidence capture.

---

## 🎯 Goals

> [!TIP]
> **This repo is built to teach.** It is an open educational resource for anyone starting a career in IT, system administration, or Microsoft endpoint management. Fork it, share it, use it in classrooms, take it to interviews.

**Why this exists**

- 📚 **Learn by real-world example.** Each deployment maps to a business reason, not "because the tool can do it."
- 🏗️ **Rebuildable from scratch.** Every phase is documented so a learner can replicate the lab end to end.
- 🌍 **Free to share.** No paywall, no gated content. Use it however helps you grow.

**Who it is for**

- 👨‍💻 Aspiring sysadmins, helpdesk pros, and tier-2 desktop engineers
- 🎓 IT bootcamp and community college students
- 🔁 Career switchers building their first technical portfolio
- 🧑‍🏫 Instructors looking for a turn-key SCCM and WSUS teaching scenario

---

## 🏢 The fictional company

**Meridian Freight Co.** is a regional logistics provider. 120 employees, two sites (HQ in Tacoma, warehouse satellite in Kent). The lab simulates the workload of a one-engineer IT shop standing up endpoint management, patching, OS deployment, and application delivery from scratch.

<table>
<tr><td>👥 <b>Employees</b></td><td>120</td></tr>
<tr><td>📍 <b>Sites</b></td><td>HQ (95) + Warehouse (25)</td></tr>
<tr><td>🏭 <b>Industry</b></td><td>Regional freight forwarding</td></tr>
<tr><td>👤 <b>IT staff</b></td><td>1 systems engineer (the role this lab represents)</td></tr>
</table>

📖 See [docs/01-scenario.md](docs/01-scenario.md) for the full business story and drivers.

---

## 🛠️ Stack

| Layer | Technology |
|------|------------|
| 🖥️ Hypervisor | Hyper-V on Windows 11 Pro |
| 🪟 Server OS | Windows Server 2022 |
| 💻 Client OS | Windows 11 |
| 🆔 Identity | Active Directory Domain Services + DNS |
| 🗄️ Database | SQL Server 2022 Standard |
| 📦 Endpoint management | Microsoft Configuration Manager (current branch) |
| 🔄 Patching | WSUS as the SCCM Software Update Point |
| 🖨️ Print | Windows Print and Document Services |
| ⚙️ Automation | PowerShell (idempotent, parameterized) |

---

## 📋 What the lab covers

<details open>
<summary><b>Click to expand the full scope</b></summary>

- AD forest build, OU structure, group policy (security baselines, drive mapping, printer mapping)
- SQL Server prerequisites for SCCM (collation, SPNs, service accounts)
- SCCM primary site install, boundary groups, client push, role distribution
- WSUS / SUP configuration, ADR (Automatic Deployment Rules) for monthly patching
- Windows 11 OS deployment via PXE (custom thin image)
- Application packaging and deployment (Microsoft 365, Google Chrome, PDF reader)
- Printer deployment via SCCM and GPO comparison
- RBAC inside SCCM (helpdesk role, server admin role)

</details>

---

## 🗂️ Repo map

```text
.
├── README.md          This file
├── docs/              Scenario, architecture, runbook, deployments, lessons
├── diagrams/          Topology and AD structure diagrams
├── screenshots/       Build evidence, console captures
├── scripts/           PowerShell automation (idempotent, parameterized)
├── gpo-exports/       Backed-up GPOs as XML
├── local_ONLY/        Working files excluded from git (see folder README)
├── LICENSE            MIT
└── .gitignore
```

---

## 📚 Documentation index

| # | Doc | What is inside |
|---|-----|----------------|
| 01 | [Scenario](docs/01-scenario.md) | Company story, departments, business drivers |
| 02 | [Architecture](docs/02-architecture.md) | Topology, IP plan, naming convention, AD design, SCCM site |
| 03 | [Build runbook](docs/03-build-runbook.md) | Phase-by-phase build checklist |
| 04 | [Deployments](docs/04-deployments.md) | Application and OS deployment scenarios with business reasons |
| 05 | [Lessons learned](docs/05-lessons-learned.md) | Real gotchas captured during the rebuild |

---

## 👤 Author

**Ajay Angdembe.** Built as a portfolio piece and a teaching resource.

Find more of my work on [GitHub](https://github.com/Aj-Networks).

---

## 📄 License

MIT. See [LICENSE](LICENSE). Use this freely for learning, teaching, or building your own lab.

<div align="center">

⭐ If this repo helps you learn or teach, consider giving it a star.

</div>
