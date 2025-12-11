# sdcard_to_sds

# Seismic Upload Workflow (SD Cards → VM → Long-Term SDS)

This repository does **not** store seismic data.  
It only tracks:

- The **commands** used to ingest data from SD cards
- A **log** of uploads (what was ingested, when, from where)
- Simple **helper scripts** (in `scripts/`)

All actual waveform data live in:

- `inbox/` (temporary, shared mount; not tracked by git)
- A local SDS archive on the VM
- The long-term SDS repository (rsync target)

---

## Directory layout

- `inbox/`  
  Temporary location for raw data copied from SD cards.  
  This is on the shared/mounted disk, visible to both Mac and VM, and **ignored by git**.

- `scripts/`  
  Helper scripts, e.g. `process_inbox_example.sh`, showing how to:
  - run `scart` into a local SDS archive
  - `rsync` that SDS into the long-term repository

- `README.md`  
  This file. Contains:
  - Workflow notes
  - A log of each upload session

---

## Useful commands

