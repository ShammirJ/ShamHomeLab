# Phase 1 — Virtualization

## Overview

The first phase of the home lab project establishes the virtualization
platform that will host the lab's virtual machines and services.

Proxmox VE was installed directly on the server as a bare-metal
hypervisor, allowing virtual machines and containers to be centrally
created, managed, and monitored.

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

After installation, the Proxmox web interface was successfully
accessed from another computer on the local network.

## Package Repository Configuration

Because this server is being used as a non-production home lab,
the subscription-only Proxmox Enterprise repositories were disabled.

The `pve-no-subscription` repository was enabled instead, allowing
the system to receive Proxmox VE package updates without an
enterprise subscription.

After configuring the repositories, the host was fully updated and
rebooted to activate the updated kernel.

## Host Networking

The Proxmox host uses the static management address:

`10.0.0.50/24`

with the default gateway:

`10.0.0.1`

The physical Ethernet interface `nic0` is attached to the Linux
bridge `vmbr0`.

### Network Architecture

Home Router / Gateway (`10.0.0.1`)
|
| Ethernet
|
`nic0`
|
`vmbr0`
|
Proxmox Host (`10.0.0.50`)
|
Future Virtual Machines

The Linux bridge allows virtual machines to connect through the
server's physical Ethernet interface and communicate with the
physical LAN.

## Current Progress

- [x] Install Proxmox VE
- [x] Configure package repositories and update host
- [x] Configure host networking
- [ ] Configure storage
- [ ] Configure remote administration
