# OpenStack Kolla-Ansible Private Cloud Lab

This repository contains my hands-on OpenStack private cloud deployment and cloud-operations learning lab.

## Project

Single-node OpenStack 2025.2 deployment using Kolla-Ansible, Docker, KVM/QEMU/libvirt, Neutron, Open vSwitch, Cinder, LVM and iSCSI on BOSS GNU/Linux 10.

## Repository Contents

- Kolla-Ansible configuration
- Ansible inventory
- OpenStack deployment configuration
- OpenStack networking configuration
- Cinder LVM storage configuration
- iSCSI troubleshooting notes
- Infrastructure commands and operational procedures
- Architecture documentation
- Cloud operations learning notes

## Architecture

```text
User
  |
  +--> Horizon / OpenStack CLI
  |
  +--> Keystone
  |
  +--> Nova --> libvirt --> QEMU/KVM --> Ubuntu VM
  |
  +--> Neutron --> Open vSwitch --> VM Networking
  |
  +--> Glance --> VM Images
  |
  +--> Cinder --> LVM --> iSCSI --> Nova/libvirt --> VM
```

## Main Lab Configuration

- Host: BOSS GNU/Linux 10
- OpenStack: 2025.2
- Kolla-Ansible: 21.0.0
- Docker container runtime
- KVM/QEMU/libvirt virtualization
- Open vSwitch networking
- Cinder LVM backend
- iSCSI block storage
- Host IP: 10.184.38.74
- OpenStack VIP: 10.184.38.75
- Internal network: 192.168.100.0/24

## VM

- Name: ubuntu-vm
- Fixed IP: 192.168.100.32
- Floating IP: 10.184.38.218
- Flavor: lab.small
- vCPU: 1
- RAM: 2048 MB

## Storage

- Cinder volume: lab-volume
- Size: 5 GB
- Backend: LVM
- Volume group: cinder-volumes
- Transport: iSCSI

## Important

Sensitive files and credentials are intentionally excluded from this repository. Configuration containing passwords, private keys, tokens or local secrets must never be committed.

## Current Status

The OpenStack platform, compute, networking and Cinder LVM backend are operational. The remaining storage task is completing and verifying the final iSCSI volume attachment so that the 5 GB Cinder volume appears as /dev/vdb inside the Ubuntu VM.
