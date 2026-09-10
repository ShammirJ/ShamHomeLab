# ShamHomeLab

A personal home lab environment designed to develop hands-on experience with virtualization, systems administration, networking, Windows Server, Linux, containerization, and infrastructure monitoring.

This repository documents the design, deployment, configuration, and troubleshooting of the environment as it is built.

## Project Goals

The goal of this project is to build and administer a small virtualized environment that simulates common IT infrastructure and provides practical experience with:

- Virtualization and virtual machine management
- Windows Server administration
- Active Directory Domain Services (AD DS)
- DNS and network configuration
- Group Policy
- Linux server administration
- Docker and containerized services
- Infrastructure monitoring
- Backup and recovery
- Technical documentation and troubleshooting

## Planned Architecture

The environment will use Proxmox VE as the primary hypervisor running multiple Windows and Linux virtual machines.

```text
Proxmox VE
│
├── Windows Server
│   ├── Active Directory Domain Services
│   └── DNS
│
├── Windows Client
│   └── Domain-joined workstation
│
└── Ubuntu Server
    └── Docker
        ├── Portainer
        └── Uptime Kuma
```

The architecture may change as the project develops and additional services are introduced.

## Hardware

| Component | Specification    |
| --------- | ---------------- |
| CPU       | TBD              |
| Memory    | TBD              |
| Storage   | TBD              |
| Network   | Gigabit Ethernet |

## Technologies

### Virtualization

- Proxmox VE

### Windows Infrastructure

- Windows Server
- Active Directory Domain Services
- DNS
- Group Policy
- Windows client

### Linux & Containers

- Ubuntu Server
- Docker
- Portainer

### Monitoring

- Uptime Kuma

### Administration

- SSH
- Remote Desktop (RDP)
- Proxmox Web Interface

## Project Roadmap

### Phase 1 — Virtualization

- [x] Install Proxmox VE
- [x] Configure host networking
- [x] Configure storage
- [x] Configure remote administration

[View Phase 1 documentation](docs/phase-1-virtualization.md)

### Phase 2 — Linux Server

- [x] Deploy Ubuntu Server VM
- [x] Configure static addressing
- [x] Configure SSH
- [ ] Install Docker
- [ ] Deploy Portainer
- [ ] Deploy Uptime Kuma

### Phase 3 — Windows Infrastructure

- [ ] Deploy Windows Server VM
- [ ] Install Active Directory Domain Services
- [ ] Configure DNS
- [ ] Create organizational units, users, and security groups
- [ ] Deploy Windows client VM
- [ ] Join Windows client to the domain
- [ ] Configure Group Policy

### Phase 4 — Administration & Reliability

- [ ] Configure shared resources and permissions
- [ ] Configure VM snapshots and backups
- [ ] Test backup and recovery procedures
- [ ] Configure infrastructure monitoring
- [ ] Document troubleshooting scenarios

### Phase 5 — Documentation

- [ ] Create network diagram
- [ ] Create infrastructure diagram
- [ ] Document Proxmox configuration
- [ ] Document Active Directory configuration
- [ ] Document Docker services
- [ ] Document major troubleshooting scenarios

## Documentation

Detailed documentation will be added as the environment is built.

```text
ShamHomeLab/
├── README.md
└── docs/
    ├── phase-1-virtualization.md
    └── images/
        ├── proxmox-dashboard.png
        └── proxmox-network.png
```

## Current Status

**In Development**
