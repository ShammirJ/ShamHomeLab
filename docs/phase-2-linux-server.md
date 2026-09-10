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

## Phase 2 Progress

- [x] Deploy Ubuntu Server VM
- [x] Configure static addressing
- [ ] Configure SSH
- [ ] Install Docker
- [ ] Deploy Portainer
- [ ] Deploy Uptime Kuma
