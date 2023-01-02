## VMs
1. named after Japanese letters

## TrueNAS
1. TrueNas scale (based on Debian)
2. 1 Volume with ZFS mirror
3. /media for Plex
4. /docker for Docker configs

## Networking??

## Containers
1. Portainer
2. 

# Things to Do
1. Build Server except of drives (include GPU)
2. Copy Plex folder to 2 TB drive (via SFTP)
3. Stop Docker
4. Copy files to 2 TB drive (via SFTP)
5. Shutdown 
6. Transfer SSD & HDDs
7. Boot from USB & install proxmox
8. [Enable IOMMU](https://pve.proxmox.com/wiki/Pci_passthrough#AMD_CPU) (for GPU pass-through)
9. Restart
10. [TrueNAS](https://youtu.be/_sfddZHhOj4?t=928) 
	1. Create TrueNAS VM
	2. [Pass HDDs to TrueNAS](https://youtu.be/M3pKprTdNqQ?t=548)
	3. Install TrueNAS on SSD
	4. Login to Web interface
	5. Create as Pool (ZFS mirror)
	6. Add Dataset to Pool
	7. Add new user to TrueNAS
	8. Setup SMB share
	9. Setup Snapshots
	10. Setup Sync Tasks
11. [Install Ubuntu](https://youtu.be/_sfddZHhOj4?t=1740)
	1. Create Ubuntu VM
	2. [Pass GPU to VM](https://youtu.be/_sfddZHhOj4?t=1068)
	3. [Pass HDDs to TrueNAS](https://youtu.be/M3pKprTdNqQ?t=548)
	4. 