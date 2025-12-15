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

## Example usage

*Gecko files arae minte long by default. Adding these to and SMB mounted disk is slow. In this example, we concat. the miniseed files to day long files using merge_days_gecko.sh *

```
#from this repo (on local filesystem) run the follwoing where BGT2 is teh name of the SD card, ./inbox/BGT2/
./scripts/merge_days_gecko.sh /Volumes/BGT2/data ./inbox/BGT2/


#then rsync to this repo on mounted disk 
rsync -avh --progress inbox/BGT2 /Volumes/proj-6700_uom_seismic_data-1128.4.1143/sdcard_to_sds/inbox

#then on VM, (this repo on mounted disk) run:
#note location code remapping here (it's quite easy to not set Geck loc code correctly, so this will homogenise to 00)
for f in ~/mnt/sdcard_to_sds/inbox/BGT2/*.mseed; do
    echo "Processing $f"

    cat "$f" \
    | scmssort -u -E 2>/dev/null \
    | scart -I - \
        --rename "Z1.BGT2.*.*:Z1.BGT2.00.-" \
        ~/mnt/sdcard_to_sds/local_sds
done

```
In the above example, it would be nice to eliminet the write to local filesystem, for instance: 

```
time ./scripts/merge_days_gecko.sh /Volumes/BGT3/data /Volumes/proj-6700_uom_seismic_data-1128.4.1143/sdcard_to_sds/inbox/BGT3/
```
But this comes as a signficant performance cost (at least 10 times) 

How about writing to a local external HD?

## Next steps

Given the perfomane limitations of transferring many small files, it's probablt worth runnign everything locally in one step, this means building seiscomp on teh mac, and then - instread of mioniseed concat, just use scarty to build a local SDS folder and copy that to the mount. Will explore using anintemediater external HD. 


https://github.com/seiscomp-macOS/seiscomp

