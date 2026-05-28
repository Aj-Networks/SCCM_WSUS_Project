# 01. The scenario

## Company

**Meridian Freight Co.** is a regional freight forwarder operating in the Pacific Northwest. The company moves palletized freight between distributors, retailers, and warehouses. Customers include grocery chains, e-commerce fulfillment centers, and a handful of industrial accounts.

* Founded: 2011
* Employees: 120
* Locations:
  * **HQ (Tacoma, WA)**: 95 employees. Dispatch, sales, accounting, leadership, IT.
  * **Warehouse satellite (Kent, WA)**: 25 employees. Yard ops, dock supervisors, a small admin pod.
* Revenue: roughly 28 million USD annually
* IT staff: one full-time systems engineer (the role this lab represents), one part-time helpdesk contractor

## Why this matters for IT

Meridian grew fast. Endpoints were provisioned ad-hoc by whichever engineer happened to be on shift. There is no consistent image, no patching SLA, no application packaging story, and no inventory. The previous engineer left and handed over a spreadsheet and a folder of ISO files.

Leadership has authorized a six-month project to bring endpoint management under control. The goals, in priority order:

1. **Inventory.** Know what is on the network and who has it.
2. **Patch compliance.** Hit a 95 percent within-30-days target for Microsoft updates.
3. **OS deployment.** Re-image a workstation in under 45 minutes from PXE.
4. **Application standardization.** Microsoft 365, Chrome, and a PDF reader on every endpoint. No more user-installed software for production tools.
5. **Repeatability.** Anyone who reads the runbook can rebuild the environment.

## Business drivers behind each deployment

The lab does not deploy software for the sake of demoing SCCM. Each deployment maps to a real reason:

* **Microsoft 365**: Accounting and Management run on Excel and Outlook all day. Standardizing the version stops "it works on my machine" tickets when sharing financial workbooks.
* **Google Chrome**: Dispatch and warehouse ops use a browser-based TMS (transportation management system). Edge has had two compatibility issues with the vendor's portal. Chrome is the supported browser per the vendor SLA.
* **PDF reader (Foxit or Acrobat Reader DC)**: Management signs vendor contracts and customer rate confirmations daily. They need a consistent reader with predictable behavior.
* **Windows 11 thin image**: The dispatch fleet runs on a 4 year old hardware refresh. Imaging speed matters because dispatch consoles cannot be down for more than an hour during shift change.
* **Monthly patch ADR**: Cyber insurance carrier now requires a documented patch process with proof of compliance reporting. No ADR, no policy renewal.
* **Printer deployment via SCCM and GPO**: Accounting needs the high-volume MFP, Management needs the executive color laser, and dock supervisors need the warehouse label printer. Print sprawl was a top helpdesk ticket category.

## Departments and people

* **IT (3 users in lab):** sysadmin, helpdesk contractor, IT manager
* **Accounting (4 users):** AP, AR, payroll, controller
* **Management (4 users):** CEO, COO, VP of Ops, VP of Sales
* **Dispatch (6 users):** dispatchers and load planners
* **Sales (6 users):** account executives and sales support
* **Warehouse (7 users):** dock supervisors, yard ops, warehouse admin

User accounts and OUs are seeded from [scripts/seed-users.csv](../scripts/seed-users.csv) (created in a later iteration).

## What this lab does NOT simulate (and why)

Honest scope statement so reviewers do not expect what is not here:

* **No second domain controller.** A real prod build for 120 users would have two DCs minimum. The lab uses one for simplicity. The runbook notes where the second DC would slot in.
* **No off-site backup or DR.** Backup strategy is mentioned in lessons learned but not implemented in the lab.
* **No co-managed Intune.** The lab is on-prem SCCM only. Co-management is called out as a logical next phase, not built.
* **No Azure AD Connect.** Out of scope. The fictional company is fully on-prem AD for now.
