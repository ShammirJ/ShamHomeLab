# Phase 2 — Linux Server

## Overview

The second phase of the home lab project focuses on deploying and configuring a Linux server that will host containerized applications and services.

An Ubuntu Server virtual machine was deployed on the Proxmox VE host. The VM is connected to the home LAN through the Proxmox Linux bridge and can be administered remotely using SSH.

## Ubuntu Server VM Deployment

Ubuntu Server was deployed as a virtual machine on the Proxmox VE host.

The VM was configured with the following networking environment:

| Setting           | Configuration   |
| ----------------- | --------------- |
| Operating System  | Ubuntu Server   |
| Hostname          | `ubuntu-server` |
| Hypervisor        | Proxmox VE      |
| Network Interface | `ens18`         |
| Proxmox Bridge    | `vmbr0`         |
| IPv4 Subnet       | `10.0.0.0/24`   |
| Default Gateway   | `10.0.0.1`      |
| Proxmox Host      | `10.0.0.50`     |
| Ubuntu Server     | `10.0.0.51`     |

After installation, the server was successfully accessed remotely from a Windows workstation using SSH.

```bash
ssh shammir@10.0.0.51
```

## Static IP Configuration

The Ubuntu Server initially received the IPv4 address `10.0.0.95/24` dynamically through DHCP.

The active network interface was identified using:

```bash
ip addr
```

The routing table was inspected using:

```bash
ip route
```

This confirmed that the VM was using the `ens18` interface and the router at `10.0.0.1` as its default gateway.

Ubuntu Server uses Netplan for network configuration. The existing configuration was located at:

```text
/etc/netplan/50-cloud-init.yaml
```

The original configuration used DHCP:

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: true
```

The configuration was changed to assign the server the static IPv4 address `10.0.0.51/24`:

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 10.0.0.51/24
      routes:
        - to: default
          via: 10.0.0.1
      nameservers:
        addresses:
          - 10.0.0.1
```

The new configuration was tested using:

```bash
sudo netplan try
```

Using `netplan try` allowed the network configuration to be temporarily applied before it was confirmed, reducing the risk of permanently losing remote SSH access because of an incorrect network configuration.

## Verification

The static address was verified with:

```bash
ip addr show ens18
```

The interface reported:

```text
inet 10.0.0.51/24
```

The routing table was also checked to verify that traffic was routed through the LAN gateway.

```bash
ip route
```

Connectivity to the local gateway was tested with:

```bash
ping -c 4 10.0.0.1
```

Internet connectivity and DNS resolution were verified with:

```bash
ping -c 4 google.com
```

All connectivity tests completed successfully.

Finally, remote administration was tested from the Windows workstation using the server's new static address:

```powershell
ssh shammir@10.0.0.51
```

The Ubuntu Server VM is now consistently reachable at `10.0.0.51` and is ready for further server configuration.

## SSH Configuration

SSH was configured to provide secure remote administration of the Ubuntu Server VM from a Windows workstation.

The OpenSSH server was verified to be running using:

```bash
sudo systemctl status ssh
```

The server was also confirmed to be listening for SSH connections on TCP port `22`.

```bash
sudo ss -tlnp | grep :22
```

### SSH Key Authentication

An existing Ed25519 SSH key pair on the Windows workstation was used for authentication.

The public key was copied to the Ubuntu Server and stored in:

```text
/home/shammir/.ssh/authorized_keys
```

The `.ssh` directory and `authorized_keys` file were configured with appropriate permissions:

```text
~/.ssh                 700
~/.ssh/authorized_keys 600
```

After installation of the public key, remote access was tested from Windows:

```powershell
ssh shammir@10.0.0.51
```

The connection succeeded without requiring the Ubuntu user's password, confirming that public-key authentication was working.

The private Ed25519 key remains on the Windows workstation and is never transferred to the server.

### SSH Hardening

SSH was hardened by disabling remote root login and password-based authentication while retaining public-key authentication.

During configuration, the effective SSH settings were inspected using:

```bash
sudo sshd -T | grep -E 'passwordauthentication|pubkeyauthentication|permitrootlogin'
```

Although `PasswordAuthentication no` had been configured in `/etc/ssh/sshd_config`, the effective configuration still reported:

```text
passwordauthentication yes
```

Further investigation identified a cloud-init-generated configuration file:

```text
/etc/ssh/sshd_config.d/50-cloud-init.conf
```

containing:

```text
PasswordAuthentication yes
```

Because SSH configuration snippets are processed before later settings in the main configuration and the first obtained value is used, the cloud-init setting was taking precedence.

A dedicated hardening configuration was therefore created:

```text
/etc/ssh/sshd_config.d/00-hardening.conf
```

with:

```text
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
```

The configuration was validated before reloading the SSH service:

```bash
sudo sshd -t
```

The effective configuration was then verified:

```text
permitrootlogin no
pubkeyauthentication yes
passwordauthentication no
```

Finally, the SSH service was reloaded:

```bash
sudo systemctl reload ssh
```

### Verification

Public-key authentication was tested from the Windows workstation and successfully connected without requesting the Ubuntu account password:

```powershell
ssh shammir@10.0.0.51
```

Password-only authentication was then explicitly tested:

```powershell
ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no shammir@10.0.0.51
```

The server rejected the connection:

```text
Permission denied (publickey).
```

This confirms that password authentication is disabled and remote administration requires possession of an authorized SSH private key.

## Phase 2 Progress

- [x] Deploy Ubuntu Server VM
- [x] Configure static addressing
- [x] Configure SSH
- [ ] Install Docker
- [ ] Deploy Portainer
- [ ] Deploy Uptime Kuma
