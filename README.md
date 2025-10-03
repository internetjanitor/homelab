# Homelab Ansible Playbooks

Ansible playbooks for managing homelab infrastructure, starting with the "nebula" server.

## Features

- ZFS storage setup and management
- Podman with ZFS storage driver
- libvirt/KVM virtualization
- Cockpit web management interface
- Let's Encrypt SSL certificates with Cloudflare DNS validation
- Common system configuration

## Prerequisites

- Arch Linux server with:
  - SSH servuce enabled
     - `systemctl enable --now sshd`
  - UFW firewall with SSH allowed
     - `ufw allow ssh`
  - SSH key configured
     - `ssh-copy-id nebula`
  - User with passwordless sudo access
     - `visudo`
       uncomment `%wheel ALL=(ALL:ALL) NOPASSWD: ALL`
     - `rm /etc/sudoers.d/10-installer`
  - ZFS pool "pool0" ready to import

## Usage

1. Install Ansible collections:
   ```bash
   ansible-galaxy collection install -r requirements.yml
   ```

2. Update inventory with your server's IP/hostname:
   ```bash
   vim inventory.yml
   ```

3. Set up Cloudflare API token for Let's Encrypt (optional):
   ```bash
   # Copy example vault file
   cp group_vars/all/vault.yml.example group_vars/all/vault.yml
   
   # Edit and encrypt the vault
   ansible-vault create group_vars/all/vault.yml
   ```

4. Run the playbook:
   ```bash
   # Without vault
   ansible-playbook site.yml
   
   # With vault for Let's Encrypt
   ansible-playbook site.yml --ask-vault-pass
   ```

## What Gets Configured

- **ZFS**: Imports "pool0" pool, creates datasets for docker, vms, and data
- **Podman**: Installed with ZFS storage driver, rootless containers support, Docker compatibility
- **libvirt**: KVM/QEMU with UEFI support, storage pool on ZFS
- **Cockpit**: Web interface on port 9090 with modules for VMs, containers, and storage
- **Let's Encrypt**: Automated SSL certificate management with Cloudflare DNS validation
- **Common**: System updates, NTP, timezone, performance tuning

## Accessing Services

- Cockpit: https://nebula:9090
- Podman: Available for rootless containers, Docker-compatible socket at /var/run/docker.sock
- libvirt: Accessible via virt-manager or virsh

## Notes

- The playbook is idempotent - safe to run multiple times
- Cockpit ZFS module requires AUR access (set `install_aur_packages: true` in inventory)
