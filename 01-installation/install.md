# 01 - Lab Installation & Setup

## Overview
Documents the full setup of the virtualized home lab environment
on a Fedora 43 Workstation using KVM and virt-manager.

## Host Machine Specs
- OS: Fedora 43 Workstation
- Window Manager: Hyprland
- Hypervisor: KVM / QEMU
- VM Manager: virt-manager

## Virtual Machines Created

| VM | OS | Role | IP |
|---|---|---|---|
| DC01 | Windows Server 2022 | Domain Controller | 192.168.122.10 |
| HomelabV1 | Windows 11 Pro | Domain Client | 192.168.122.42 |

## Installation Steps

### 1. Install KVM and virt-manager on Fedora
```bash
sudo dnf install @virtualization
sudo systemctl enable --now libvirtd
sudo usermod -aG libvirt $USER
```

### 2. Create Windows Server 2022 VM
- Allocated 4GB RAM, 2 vCPUs, 60GB disk
- Mounted Windows Server 2022 ISO
- Completed installation and initial setup
- Renamed machine to DC01

### 3. Install Active Directory Domain Services
- Server Manager → Add Roles → AD DS
- Promoted server to Domain Controller
- Created new forest: homelab.local
- Configured DNS during promotion

### 4. Create Windows 11 Pro VM
- Allocated 4GB RAM, 2 vCPUs, 40GB disk
- Mounted Windows 11 Pro ISO
- Completed installation
- Renamed machine to HomelabV1

### 5. Join Windows 11 to Domain
- Settings → System → Domain/Workgroup
- Joined homelab.local using DC credentials
- Restarted and confirmed domain login

### 6. Verify Lab is Functional
- Confirmed AD DS, DNS services running on DC
- Confirmed client resolves homelab.local
- Mapped network drive Z: via GPO
- Verified domain login on HomelabV1