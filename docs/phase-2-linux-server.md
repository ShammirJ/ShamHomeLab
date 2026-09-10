# Phase 2 — Linux Server

## Overview

The second phase of the home lab project focuses on deploying a Linux server
within the Proxmox virtualization environment established in Phase 1.

An Ubuntu Server virtual machine was deployed to provide a general-purpose
Linux environment for hosting containerized services. This VM will later be
configured with Docker, Portainer, and Uptime Kuma.

## Ubuntu Server VM

Ubuntu Server 24.04 LTS was deployed as a virtual machine on the Proxmox host.

### Virtual Machine Configuration

| Setting          | Configuration           |
| ---------------- | ----------------------- |
| VM ID            | `100`                   |
| VM Name          | `ubuntu-server`         |
| Operating System | Ubuntu Server 24.04 LTS |
| CPU              | 2 vCPUs                 |
| Memory           | 4 GiB                   |
| Virtual Disk     | 64 GiB                  |
| Disk Storage     | `local-lvm`             |
| Disk Controller  | VirtIO SCSI             |
| Network Adapter  | VirtIO                  |
| Network Bridge   | `vmbr0`                 |
| QEMU Guest Agent | Enabled                 |

The Ubuntu installation ISO was stored on Proxmox `local` storage while the
VM's virtual disk was created on the `local-lvm` thin-provisioned storage pool.

## Storage Configuration

Ubuntu was installed using the guided LVM storage configuration.

The 64 GiB virtual disk was divided into a boot partition and an LVM physical
volume. Ubuntu created the `ubuntu-vg` volume group with the root filesystem
stored on the `ubuntu-lv` logical volume.

Approximately half of the volume group remains unallocated, allowing the root
logical volume to be expanded or additional logical volumes to be created in
the future.

Disk encryption was not enabled for this VM.

## Networking

The VM is connected to the existing Proxmox Linux bridge `vmbr0` using a
VirtIO virtual network adapter.

During installation, the Ubuntu interface `ens18` received the following
address through DHCP:

| Setting            | Value          |
| ------------------ | -------------- |
| Interface          | `ens18`        |
| IPv4 Address       | `10.0.0.95/24` |
| Address Assignment | DHCP           |
| Network            | `10.0.0.0/24`  |

The DHCP address is temporary and will be replaced with a permanent address
during the static addressing configuration.

Network connectivity was verified by successfully reaching both an external
IP address and a DNS hostname with no packet loss.

## Remote Administration

The OpenSSH Server package was installed during the Ubuntu installation.

Remote access from a Windows workstation was successfully tested using:

`ssh shammir@10.0.0.95`

The SSH connection successfully authenticated using the Ubuntu user's
password and provided a remote shell on `ubuntu-server`.

SSH key-based authentication will be configured later using an Ed25519 key
pair.

## Verification

The initial Ubuntu Server deployment was verified by confirming:

- The system hostname is `ubuntu-server`.
- The `ens18` network interface is operational.
- The VM received an IPv4 address from DHCP.
- Internet connectivity is functional.
- DNS resolution is functional.
- Remote SSH access from the administration workstation is functional.

## Phase 2 Progress

- [x] Deploy Ubuntu Server VM
- [ ] Configure static addressing
- [ ] Configure SSH
- [ ] Install Docker
- [ ] Deploy Portainer
- [ ] Deploy Uptime Kuma
