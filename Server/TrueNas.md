# TrueNas

This is a VM running on Proxmox. More about the hardwareconfiguration con be found in the Proxmox documentation.
It is using TrueNas Core as ist OS.

TrueNas serves as the main storage device in my Infrastructure.
The two 4TB HDDs are configured in a ZFS mirror (similar to Raid 1), therefore there is very little CPU utilisation (no parity bits requiered).

## storage pool

The storage is set up in a single storage pool with tree diffrent datasets. Each of these datasets has its own SMB share.

### Dataset 1 (prox-share)
This dataset is the main place for everyday usage. Its is split into three diffrent folders:
* Docker: This folder acts as a mass storage device for docker containers e.g. mediastorage for Photoprism
* Media: This folder is the place where all Videofiles for Plex media server are stored
* Nextcloud: This folder contains the files located within Nextcloud e.g. documents, photos, etc.

#### Backups
Backups of this dataset are made once per week over a SFTP connection to Hetzner. 

File are getting encrypted and are then syncronised with the ones on the Storage Box at Hetzner.
This way only changes have to be uploaded every week.

### Dataset 2 (Backups)
This dataset is the main storage for the daily updates created by proxmox.
More about these Backups at the proxmox documentation.

With this dataset there is no offsite Backup since these get overwritten every 4 days anyways.

### Dataset 3 (Weekly Backups)
This dataset is the seconday location of proxmox backups.
Here only the weekly backups are stored.

As soon as these are created they get encrpted and pushed to the offsite storage box.

These Backups get overwritten every week but the last state of a month is saved by the storage box snapshots.
This allows for a retention of up to 6 month back with the latest state for every month.

## Storage Box
This storage box is located at Hetzner and has a total of 5 TB of storage.

Of these 5 TB the storage box makes a snapshot once a month.
With a total of 6 snapshots this allows for a retention of up to 6 month back.

A nice visualisation of the backups structure can be found in the "Backup Strategy.pdf"

