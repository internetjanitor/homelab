# Homelab Ansible Playbooks

Ansible playbooks for managing homelab infrastructure, starting with the "nebula" server.

## Features

- ZFS storage setup and management
- Docker with ZFS storage driver
- libvirt/KVM virtualization
- Cockpit web management interface
- Common system configuration

## Prerequisites

- Arch Linux server with:
  - User "eric" with sudo access
     - `visudo`
       uncomment `%wheel ALL=(ALL:ALL) NOPASSWD: ALL`
     - `rm /etc/sudoers.d/10-installer`
  - SSH servuce enabled
     - `systemctl enable --now sshd`
  - SSH key configured
     - `ssh-copy-id nebula`
  - UFW firewall with SSH allowed
     - `ufw allow ssh`
  - ZFS pool "tank" ready to import

## Usage

1. Install Ansible collections:
   ```bash
   ansible-galaxy collection install -r requirements.yml
   ```

2. Update inventory with your server's IP/hostname:
   ```bash
   vim inventory.yml
   ```

3. Run the playbook:
   ```bash
   ansible-playbook site.yml
   ```

## What Gets Configured

- **ZFS**: Imports "tank" pool, creates datasets for docker, vms, and data
- **Docker**: Installed with ZFS storage driver, user added to docker group
- **libvirt**: KVM/QEMU with UEFI support, storage pool on ZFS
- **Cockpit**: Web interface on port 9090 with modules for VMs, containers, and storage
- **Common**: System updates, NTP, timezone, performance tuning

## Accessing Services

- Cockpit: https://nebula:9090
- Docker: Available to user "eric"
- libvirt: Accessible via virt-manager or virsh

## Notes

- The playbook is idempotent - safe to run multiple times
- Cockpit ZFS module requires AUR access (set `install_aur_packages: true` in inventory)
