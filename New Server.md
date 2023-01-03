## VMs
1. named after Japanese letters

## TrueNAS
1. TrueNas scale (based on Debian)
2. 1 Volume with ZFS mirror
3. /media for Plex
4. /docker for Docker configs

# Things to Do
1. Build Server except of drives (include GPU)
2. Copy Plex folder to 2 TB drive (via SFTP)
3. Stop Docker
4. Copy files to 2 TB drive (via SFTP)
5. Copy "/etc/prometheus" to 2 TB drive (via SFTP)
6. Shutdown 
7. Transfer SSD & HDDs
8. Boot from USB & install proxmox
9. [Enable IOMMU](https://pve.proxmox.com/wiki/Pci_passthrough#AMD_CPU) (for GPU pass-through)
10. Restart
11. [TrueNAS](https://youtu.be/_sfddZHhOj4?t=928) 
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
12. [Install Ubuntu](https://youtu.be/_sfddZHhOj4?t=1740)
	1. Create Ubuntu VM
	2. [Pass GPU to VM](https://youtu.be/_sfddZHhOj4?t=1068)
	3. [Pass HDDs to TrueNAS](https://youtu.be/M3pKprTdNqQ?t=548)
	4. Install Ubuntu on SSD
	5. Set up [[Linux]]
	6. [Mount SMB share](https://linuxhint.com/mount-smb-shares-on-ubuntu/)
	7. Install Docker
	8. Set up a [[Duplicati temp]] container
	9. Restore Backups from Docker
	10. [Install AdGuardHome](https://www.youtube.com/watch?v=B2V_8M9cjYw)
	11. Install Cockpit
	12. Start all Containers (Change file mount)
	13. Setup Traefik
		1. for all container (should be working)
		2. for AdGuardHome
		3. for cockpit
		4. for proxmox
		5. for TrueNAS
		6. for Plex
	14. Check all services (setup Fenrus)
	15. Stop [[Duplicati temp]]
13. Install VPN
	1. Create VPN VM
	2. Install Ubuntu on SSD
	3. Set up [[Linux]]
	4. Install Docker
	5. Install OpenVPN
	6. add client file to VPN
	7. start VPN
	8. check connection
	9. setup forward proxy (Traefik same as [[VPN Instance]])
	10. Secure firewall between VMs





