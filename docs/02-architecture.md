# 02. Architecture

## Topology overview

Single Active Directory forest, single domain, single SCCM primary site. Two physical sites in the fiction (HQ + warehouse); the lab represents both as VLANs on the same Hyper-V host.

```
                          [ MF-DC01 ]
                          AD DS, DNS
                              |
       +----------------------+----------------------+
       |                      |                      |
  [ MF-DB01 ]            [ MF-CM01 ]            [ MF-SUP01 ]
  SQL 2022               SCCM primary           WSUS / SUP
  SCCM site DB           MP, DP, SMS Provider
                              |
                              |
                       [ MF-PRNT01 ]
                       Print server

                              |
       +----------------------+----------------------+----------------------+
       |                      |                      |                      |
   IT clients          Accounting clients      Management clients      General clients
   (2 VMs)             (2 VMs)                 (2 VMs)                 (4 VMs)
```

## Server inventory

| VM        | Role                                    | OS                | vCPU | RAM   | Disk(s)            |
|-----------|------------------------------------------|-------------------|------|-------|--------------------|
| MF-DC01   | AD DS, DNS, primary DC                  | Windows Server 2022 | 2    | 4 GB  | 60 GB OS           |
| MF-DB01   | SQL Server 2022 (SCCM site DB)          | Windows Server 2022 | 4    | 8 GB  | 60 GB OS, 80 GB DB |
| MF-CM01   | SCCM primary site, MP, DP, SMS Provider | Windows Server 2022 | 4    | 8 GB  | 80 GB OS, 100 GB CM content library |
| MF-SUP01  | WSUS / Software Update Point             | Windows Server 2022 | 2    | 4 GB  | 60 GB OS, 80 GB updates |
| MF-PRNT01 | Print server                             | Windows Server 2022 | 2    | 2 GB  | 60 GB OS           |

Dynamic Memory enabled on client VMs only. Servers stay on static memory to avoid SCCM site role flapping.

## Client inventory

| Hostname pattern | Department  | Count | RAM (dynamic) | Notes                          |
|------------------|-------------|-------|---------------|--------------------------------|
| MF-IT-NN         | IT          | 2     | 1-4 GB        | RSAT installed, helpdesk role  |
| MF-ACCT-NN       | Accounting  | 2     | 1-3 GB        | M365 deployed                  |
| MF-MGMT-NN       | Management  | 2     | 1-3 GB        | M365 + PDF reader              |
| MF-GEN-NN        | General     | 4     | 1-2 GB        | Chrome only baseline           |

## Naming convention

`MF-<ROLE>-<NN>` or `MF-<ROLE><NN>` for servers.

* `MF`: Meridian Freight prefix (uniquely identifies the org in any future M and A scenario)
* `<ROLE>`: short role code (DC, DB, CM, SUP, PRNT, IT, ACCT, MGMT, GEN)
* `<NN>`: two digit sequence (01, 02, ...)

Examples: `MF-DC01`, `MF-CM01`, `MF-ACCT01`.

This matches the convention used by most SMBs that have outgrown ad-hoc naming but do not have the scale to need a location-role-instance pattern like `SEA-DC-01`.

## IP plan

| Subnet           | VLAN | Purpose                | Range allocated     |
|------------------|------|------------------------|---------------------|
| 10.10.10.0/24    | 10   | HQ servers             | .10 to .50          |
| 10.10.20.0/24    | 20   | HQ clients             | .20 to .250 (DHCP)  |
| 10.10.30.0/24    | 30   | Warehouse clients      | .20 to .250 (DHCP)  |
| 10.10.40.0/24    | 40   | Management network     | .10 to .50          |

Static assignments for servers:

| Host       | IP            |
|------------|---------------|
| MF-DC01    | 10.10.10.10   |
| MF-DB01    | 10.10.10.11   |
| MF-CM01    | 10.10.10.12   |
| MF-SUP01   | 10.10.10.13   |
| MF-PRNT01  | 10.10.10.14   |

Default gateway: `10.10.10.1` (Hyper-V virtual switch with NAT for outbound).

## Active Directory

* **Forest / domain:** `meridianfreight.local`
* **NetBIOS:** `MERIDIAN`
* **Forest functional level:** Windows Server 2016
* **Domain functional level:** Windows Server 2016
* **DNS:** AD-integrated, primary on MF-DC01

### OU structure

```
meridianfreight.local
├── OU=Meridian
│   ├── OU=Servers
│   │   ├── OU=Infrastructure
│   │   └── OU=Application
│   ├── OU=Workstations
│   │   ├── OU=IT
│   │   ├── OU=Accounting
│   │   ├── OU=Management
│   │   ├── OU=Dispatch
│   │   ├── OU=Sales
│   │   └── OU=Warehouse
│   ├── OU=Users
│   │   ├── OU=Employees
│   │   │   └── (mirrored department OUs)
│   │   └── OU=ServiceAccounts
│   └── OU=Groups
│       ├── OU=Security
│       └── OU=Distribution
```

Built-in OUs (`Users`, `Computers`) are left empty by design. The redirect of new computer accounts is set via `redircmp` to `OU=Workstations,OU=Meridian,DC=meridianfreight,DC=local`.

### Service accounts

| Account             | Purpose                                  | Membership                       |
|---------------------|------------------------------------------|----------------------------------|
| svc_sccm_naa        | SCCM Network Access Account              | Domain Users                     |
| svc_sccm_join       | Domain join during OSD                   | Domain Users + delegated         |
| svc_sccm_clientpush | Client push install                      | Local admin on workstations OU   |
| svc_sql_engine      | SQL Server service account               | Domain Users                     |
| svc_sql_agent       | SQL Agent service account                | Domain Users                     |
| svc_wsus            | WSUS app pool                            | Domain Users                     |

All service accounts have:
* "Password never expires" set
* Long random passwords stored in the engineer's password manager
* Logon restrictions (logon-to-specific-computers) where applicable
* No interactive logon rights on workstations

### Group Policy

Initial GPO set built in the lab:

* `MF-Baseline-Workstations`: security baseline (firewall, BitLocker, UAC, screen lock)
* `MF-Baseline-Servers`: server baseline (RDP restrictions, audit policy)
* `MF-Workstation-DriveMap`: maps `H:` (home) and `S:` (department share)
* `MF-Workstation-PrinterMap`: printer deployment by department
* `MF-SCCM-Client-Settings`: firewall exceptions, WMI, file/print sharing for client push

## SCCM site

* **Site code:** `MER`
* **Site name:** Meridian Freight Primary
* **Site type:** Primary site (standalone)
* **Site server:** MF-CM01
* **Site database:** `MF-DB01\MSSQLSERVER`, instance `default`, DB name `CM_MER`
* **Management point:** MF-CM01
* **Distribution point:** MF-CM01 (PXE enabled)
* **SMS Provider:** MF-CM01
* **Software Update Point:** MF-SUP01 (WSUS upstream = Microsoft Update)

Boundary groups:
* `HQ-Servers`: 10.10.10.0/24
* `HQ-Clients`: 10.10.20.0/24
* `WH-Clients`: 10.10.30.0/24
