# Phase 1 — Virtualization

## Overview

The first phase of the home lab project establishes the virtualization
platform that will host the lab's virtual machines and services.

Proxmox VE was installed directly on the server as a bare-metal
hypervisor, allowing virtual machines and containers to be centrally
created, managed, and monitored.

---

## Proxmox VE Installation

Proxmox VE 9 was installed as the primary operating environment for
the home lab server.

The installation was configured with:

| Setting              | Configuration  |
| -------------------- | -------------- |
| Hypervisor           | Proxmox VE 9   |
| Node Name            | `pve`          |
| Management IP        | `10.0.0.50/24` |
| Gateway              | `10.0.0.1`     |
| Management Interface | `vmbr0`        |
| Physical Interface   | `nic0`         |

After installation, the Proxmox web interface was successfully accessed
from another computer on the local network.

![Proxmox VE Dashboard](images/proxmox-dashboard.png)

---

## Package Repository Configuration

Because this server is being used as a non-production home lab, the
subscription-only Proxmox Enterprise repositories were disabled.

The `pve-no-subscription` repository was enabled instead, allowing the
system to receive Proxmox VE package updates without an enterprise
subscription.

After configuring the repositories, the host was fully updated and
rebooted to activate the updated kernel.

---

## Host Networking

The Proxmox host is connected to the local network through its physical
Ethernet interface (`nic0`). Proxmox uses a Linux bridge (`vmbr0`) for
management connectivity and future virtual machine and container
networking.

### Network Configuration

| Setting              | Value          |
| -------------------- | -------------- |
| Hostname             | `pve`          |
| Management Interface | `vmbr0`        |
| Physical Interface   | `nic0`         |
| IPv4 Address         | `10.0.0.50/24` |
| Default Gateway      | `10.0.0.1`     |
| Connection           | Ethernet       |
| Address Assignment   | Static         |
| DHCP Reservation     | `10.0.0.50`    |

The physical Ethernet interface `nic0` is attached to the `vmbr0` Linux
bridge. The Proxmox management interface is assigned the static address
`10.0.0.50/24`.

A DHCP reservation for `10.0.0.50` was configured on the network gateway
to ensure the management address remains associated with the Proxmox
host and is not assigned to another device.

![Proxmox Network Configuration](images/proxmox-network.png)

### Network Verification

The network configuration was verified by:

- Confirming `vmbr0` is active and attached to `nic0`
- Confirming the Proxmox host is reachable at `10.0.0.50`
- Confirming the default gateway is `10.0.0.1`
- Reserving `10.0.0.50` for the Proxmox host on the network gateway
- Successfully accessing the Proxmox web interface from another
  computer on the LAN

---

## Storage Configuration

The Proxmox host uses a single 1 TB Kingston NVMe SSD for the
hypervisor and virtual machine storage.

The default Proxmox LVM-thin storage layout was retained because it
provides separate storage locations for installation media and
virtual machine disks without requiring additional disk configuration.

| Storage     | Type      | Purpose                                               |
| ----------- | --------- | ----------------------------------------------------- |
| `local`     | Directory | ISO images, container templates, backups, and imports |
| `local-lvm` | LVM-Thin  | Virtual machine and container disks                   |

The physical NVMe drive uses a GPT partition table and contains
dedicated BIOS boot, EFI, and LVM partitions. The drive's S.M.A.R.T.
health check reports `PASSED` with 0% wearout.

At this stage, all storage resides on a single physical drive.
Dedicated backup storage may be added in a later phase to provide
separation between production virtual machines and backups.

## Remote Administration

Remote administration was configured to allow the Proxmox host to be
managed without requiring direct physical access to the server.

Two methods of remote administration are available:

- **Proxmox Web Interface** — Provides browser-based management of
  virtual machines, containers, storage, networking, and host resources.
- **SSH** — Provides command-line access to the underlying Proxmox host.

The Proxmox management interface is accessible on the local network at:

`https://10.0.0.50:8006`

SSH connectivity was verified from a Windows administration workstation:

`ssh root@10.0.0.50`

An ED25519 SSH key pair was generated on the administration workstation.
The public key was added to the Proxmox host's `authorized_keys` file,
allowing key-based SSH authentication instead of requiring the Proxmox
root password for each connection.

The private SSH key remains stored only on the administration
workstation.

With remote administration configured, the Proxmox server can operate
headlessly without a dedicated monitor, keyboard, or mouse.

## Current Progress

- [x] Install Proxmox VE
- [x] Configure package repositories and update host
- [x] Configure host networking
- [x] Configure storage
- [x] Configure remote administration
