# 03. Build runbook

This is a placeholder skeleton. Each phase will be expanded with exact commands, screenshots, and gotchas as the lab is rebuilt for evidence capture.

## Phase 0: Host prep

* [ ] Confirm Hyper-V role installed, virtualization enabled in firmware
* [ ] Create external + internal virtual switches (NAT for outbound)
* [ ] Provision external SSD as VM storage path
* [ ] Snapshot policy: pre-build, post-AD, post-SCCM, post-deployment

## Phase 1: Domain controller (MF-DC01)

* [ ] Deploy Server 2022 VM, set hostname, static IP, time zone
* [ ] Install AD DS + DNS roles
* [ ] Promote to first DC of new forest `meridianfreight.local`
* [ ] Build OU structure per [02-architecture.md](02-architecture.md)
* [ ] Redirect new computer accounts (`redircmp`) and new users (`redirusr`)
* [ ] Create service accounts
* [ ] Seed user accounts from CSV
* [ ] Build baseline GPOs and link

## Phase 2: SQL Server (MF-DB01)

* [ ] Deploy Server 2022 VM, join domain
* [ ] Install SQL Server 2022 with collation `SQL_Latin1_General_CP1_CI_AS`
* [ ] Configure SQL service accounts (no local SYSTEM)
* [ ] Set SQL memory limits (min 4 GB, max 6 GB on 8 GB VM)
* [ ] Register SPNs for `svc_sql_engine` (Kerberos)
* [ ] Open firewall: 1433/TCP and 4022/TCP for SCCM
* [ ] Add the future CM site server computer account to SQL `sysadmin` role
* [ ] Add `svc_sccm_naa` to local SQL group as required

## Phase 3: SCCM primary site (MF-CM01)

* [ ] Deploy Server 2022 VM, join domain
* [ ] Install Windows ADK and Windows PE add-on (versions matched to current branch)
* [ ] Install WSUS feature (for SUP role co-location decision: not co-located here, SUP lives on MF-SUP01)
* [ ] Install IIS, BITS, WDS, .NET, Remote Differential Compression
* [ ] Extend AD schema (`extadsch.exe`)
* [ ] Create AD container `System Management` and delegate permissions to CM01 computer account
* [ ] Run SCCM primary site install pointing at MF-DB01
* [ ] Configure site boundaries and boundary groups
* [ ] Configure client push installation account
* [ ] Configure discovery methods (AD System, AD User, AD Group, network)

## Phase 4: WSUS / SUP (MF-SUP01)

* [ ] Deploy Server 2022 VM, join domain
* [ ] Install WSUS role, content path on dedicated drive
* [ ] Run WSUS post-install configuration
* [ ] Add MF-SUP01 to SCCM as Software Update Point
* [ ] Configure classifications and products (Windows 11, Server 2022, Office 365 client, SQL 2022)
* [ ] Configure sync schedule
* [ ] Build Automatic Deployment Rule for Patch Tuesday
* [ ] Decline superseded updates monthly

## Phase 5: Print server (MF-PRNT01)

* [ ] Deploy Server 2022 VM, join domain
* [ ] Install Print and Document Services
* [ ] Install drivers for lab printers (use Microsoft generic drivers)
* [ ] Create virtual printer ports and share three printers (Accounting MFP, Management color, Warehouse label)
* [ ] Deploy via GPO and via SCCM, compare experience

## Phase 6: Client onboarding

* [ ] Build Windows 11 reference VHDX (sysprepped, minimal apps)
* [ ] Capture as a CM image
* [ ] Build task sequence: partition, apply image, join domain, install CM client, install apps
* [ ] PXE boot a bare VM, run task sequence, validate domain join, CM client check-in, app install

## Phase 7: Application deployment

* [ ] Package and deploy Microsoft 365 Apps (XML configuration, monthly enterprise channel)
* [ ] Package and deploy Google Chrome (MSI, latest GA)
* [ ] Package and deploy PDF reader
* [ ] Validate per-department targeting via collections built from OU queries

## Phase 8: Monitoring and reporting

* [ ] Install SSRS / Reporting Services Point
* [ ] Stand up standard reports (compliance, OS distribution, app inventory)
* [ ] Build a dashboard for patch compliance
* [ ] Document SLA: 95 percent compliance within 30 days of patch release
