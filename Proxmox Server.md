This server consists of the following hardware:

CPU: Ryzen 7 3700x - 8 cores 16 threads
Memory: 32 GB DDR4 - 2400Mhz
Storage:
	SSD: 128 GB SATA ssd
	HDD1: 4 TB Western Digital Red - Raid 1
	HDD2: 4 TB Western Digital Red - Raid 1
	HDD3: 500 GB HDD
	Networking: 192.168.0.95 - 1 Gb/s

This server is running Proxmox as its operating system and currently runs three VMs.

VM100: TrueNas
	This machines task is to manage and provide the main storage device of the Server.
	It has the following Hardware:
		CPU: 2 vCores
		Memory: 8 GB
		Storage: 
			SSD: 16 GB virtuall disk
			HDD1: 4 TB Western Digital Red - Raid 1
			HDD2: 4 TB Western Digital Red - Raid 1
		Networking: 
			bridge-network: 192.168.0.90

VM101: Ubuntu-Docker
	This machine is the main computing VM. It runs all services, but requieres the services of the other VMs (storage and external access)
	It has the following Hardware:
		CPU: 12 vCores
		Memory: 20 GB
		Storage: 
			SSD: 48 GB virtuall disk
			SMB share of VM100
		Networking: 
			bridge-network: 192.168.0.91

VM102: Ubuntu-VPN
	This machine is the gateway of all internet-traffic into the local network. It has a VPN connetcion to the Arm-instance and proxies traffic between Arm-instance and VM101.
	Futhermore it passes the SMB share of VM100 to the Arm-instance.
	It has the following Hardware:
		CPU: 2 vCores
		Memory: 3 GB
		Storage: 
			HDD3: 16 GB virtuall disk
			SMB share of VM100
		Networking: 
			bridge-network: 192.168.0.92
			VPN-network: 10.8.0.2