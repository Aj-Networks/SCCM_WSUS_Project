# local_ONLY/

**Everything in this folder is ignored by git.** Drop here anything that must never reach the public repo.

This README is the one exception: it stays tracked so the folder structure is visible to anyone cloning the repo.

## What goes here

* **Credentials and secrets**
  * `.env` files, `.pfx`, `.cer`, `.key`, `.pem`
  * KeePass / Bitwarden exports (`.kdbx`, `.csv`)
  * Service account password sheets, recovery keys, BitLocker keys
  * SCCM Network Access Account password notes

* **Raw / unredacted screenshots**
  * Console captures that still show real IPs, hostnames, usernames, or tenant IDs
  * Move to `screenshots/` AFTER redacting

* **VM artifacts**
  * `.vhd`, `.vhdx`, `.avhd`, `.avhdx`
  * `.iso` files (Server 2022, Win 11, SQL, ADK)
  * Hyper-V exports

* **Working notes and scratch**
  * Personal `notes.md` with real-world context, vendor pricing, hiring-target list
  * Scratch PowerShell while testing
  * Draft outlines that aren't ready for `docs/`

* **Backups of real environments**
  * Anything copied in from a previous job or live environment
  * Real GPO exports from a production tenant

## Organize how you like

Subfolders inside `local_ONLY/` are encouraged. Suggested layout:

```
local_ONLY/
├── README.md            (tracked)
├── credentials/         (ignored)
├── notes/               (ignored)
├── raw-screenshots/     (ignored)
├── vm-exports/          (ignored)
└── isos/                (ignored)
```

## How the .gitignore is wired

The repo `.gitignore` contains:

```
local_ONLY/*
!local_ONLY/README.md
```

The first line ignores everything in the folder. The second line whitelists this README so the folder is not empty in the repo.

## Sanity check before you commit

If `git status` ever shows a file from `local_ONLY/` as staged or modified, stop and verify the .gitignore is still in place. The folder name is `local_ONLY` (capital ONLY) — case matters on case-sensitive file systems.
