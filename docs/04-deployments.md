# 04. Deployment scenarios

Each scenario lists the business reason, the SCCM mechanism, the target collection, and the validation step. Detailed configuration (XML, MSI args, screenshots) lands here as the lab progresses.

## 4.1 Microsoft 365 Apps for Enterprise

* **Driver:** Accounting and Management standardize on Excel/Outlook for daily ops. Compatibility issues with mixed channels caused a Q3 incident.
* **Mechanism:** SCCM application (script installer), source built with Office Deployment Tool and a versioned `configuration.xml`
* **Channel:** Monthly Enterprise
* **Target:** Collections `Coll-Accounting-Workstations` and `Coll-Management-Workstations` (membership queries by OU)
* **Validation:**
  * `Get-OfficeVersion` returns expected build on each target
  * SCCM Software Center shows "Installed"
  * `winword.exe` launches without activation prompts (lab uses KMS host emulated by command line activation)

## 4.2 Google Chrome (Enterprise MSI)

* **Driver:** Vendor TMS portal supports only Chrome and Firefox. Edge has had two compatibility issues. Standardize on Chrome.
* **Mechanism:** SCCM application (MSI installer)
* **Source:** `GoogleChromeStandaloneEnterprise64.msi` from Google's enterprise download
* **Args:** `/qn /norestart`
* **Target:** Collection `Coll-All-Workstations`
* **Validation:** `HKLM:\SOFTWARE\Google\Chrome\BLBeacon` present; version equals deployed build

## 4.3 PDF reader

* **Driver:** Management signs contracts and rate confs daily. Need consistent reader and predictable defaults.
* **Mechanism:** SCCM application (MSI)
* **Candidate:** Foxit PDF Reader (free) or Adobe Acrobat Reader DC. Lab uses Foxit for size and quiet install simplicity.
* **Target:** Collection `Coll-Management-Workstations`
* **Validation:** File association `.pdf` set to Foxit on target machines

## 4.4 Windows 11 OS deployment (PXE)

* **Driver:** Dispatch consoles cannot be down for more than an hour during shift change. Need a 45 minute re-image window.
* **Mechanism:** SCCM task sequence + PXE-enabled DP on MF-CM01
* **Image:** Custom Windows 11 reference VHDX, sysprepped, with .NET, VC++ redistributables, and updates slipstreamed
* **Steps:** Partition / Format / Apply OS / Apply drivers / Setup Windows / Join domain / Install CM client / Install apps (Chrome, M365 if targeted)
* **Target:** Unknown computers collection (filtered by MAC prefix to lab VMs)
* **Validation:** Bare VM PXE boots, task sequence completes, machine appears in correct OU, CM client reports back, baseline apps installed

## 4.5 Monthly patching (ADR)

* **Driver:** Cyber insurance carrier requires documented monthly patching with compliance reporting. No ADR, no policy renewal.
* **Mechanism:** SCCM Automatic Deployment Rule
* **Sync products:** Windows 11, Windows Server 2022, Office 365 Apps, SQL Server 2022
* **Classifications:** Security Updates, Critical Updates, Definition Updates
* **Schedule:** Sync second Tuesday + 4 hours
* **Deployment package:** monthly, replaced each cycle
* **Rings:**
  * Ring 1: IT workstations (1 day after sync)
  * Ring 2: Pilot users (3 days after sync)
  * Ring 3: All workstations (7 days after sync)
  * Ring 4: Servers (14 days after sync, maintenance window)
* **Validation:** Compliance report shows 95 percent or higher within 30 days; non-compliant machines have a documented exception or are escalated

## 4.6 Printer deployment

* **Driver:** Print sprawl was a top helpdesk ticket category. Standardize on one MFP per department.
* **Lab compares two methods:**
  * **GPO Printer Mapping** (Group Policy Preferences): fast to set up, user context
  * **SCCM printer deployment** (script that runs `Add-Printer`): works at machine context, audited via CM compliance
* **Printers in lab:**
  * `\\MF-PRNT01\ACCT-MFP01`: Accounting OU
  * `\\MF-PRNT01\MGMT-COLOR01`: Management OU
  * `\\MF-PRNT01\WH-LABEL01`: Warehouse OU
* **Validation:** Each user logs into a workstation, sees only the printer for their OU
