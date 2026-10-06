# OpenStack Kolla-Ansible Private Cloud Lab

Hands-on single-node OpenStack private cloud deployment using Kolla-Ansible, Docker, KVM/libvirt, Open vSwitch, LVM and iSCSI.

## Project Overview

This project documents a practical OpenStack private cloud lab deployed on BOSS GNU/Linux 10 using Kolla-Ansible and OpenStack 2025.2.

## Architecture

```text
User
 |
 +-- Horizon / OpenStack CLI
 |
 +-- Keystone - Identity
 |
 +-- Nova - Compute
 |     +-- libvirt / QEMU / KVM
 |     +-- Ubuntu VM
 |
 +-- Neutron - Networking
 |     +-- Open vSwitch
 |     +-- Router / Floating IP
 |
 +-- Glance - Images
 |
 +-- Cinder - Block Storage
       +-- LVM
       +-- Logical Volume
       +-- iSCSI Target
       +-- iSCSI Initiator
       +-- Nova Compute
       +-- libvirt
       +-- Ubuntu VM /dev/vdb
```

## Environment

- BOSS GNU/Linux 10
- OpenStack 2025.2
- Kolla-Ansible 21.0.0
- Docker
- KVM / QEMU / libvirt
- Open vSwitch
- LVM
- iSCSI
- Host IP: 10.184.38.74
- OpenStack VIP: 10.184.38.75
- Internal network: 192.168.100.0/24

## OpenStack Components

### Keystone
Identity and authentication service.

### Horizon
Web dashboard for OpenStack management.

### Nova
Compute service managing VM lifecycle through libvirt and QEMU/KVM.

### Neutron
Networking service providing networks, subnets, routers and floating IPs.

### Glance
Image service used for VM images.

### Cinder
Block storage service providing persistent volumes to instances.

## Cinder Storage Flow

```text
Cinder API
 -> Cinder Volume
 -> LVM
 -> Logical Volume
 -> iSCSI Target
 -> iSCSI Initiator
 -> Nova Compute
 -> libvirt
 -> QEMU/KVM
 -> Ubuntu VM
 -> /dev/vdb
```

## Storage

A 20 GB loopback-backed storage file was used for the Cinder LVM backend:

`/var/lib/cinder-volumes.img`

The Cinder volume created was:

`lab-volume`

Volume ID:

`8db1deca-cec2-4ee8-9cbe-f1ecabc4dfd1`

Size: 5 GB

## Networking

Internal network: `192.168.100.0/24`

Ubuntu VM fixed IP: `192.168.100.32`

Floating IP: `10.184.38.218`

Open vSwitch provides the virtual switching layer.

## Virtualization

The VM stack is:

```text
Nova
 -> libvirt
 -> QEMU/KVM
 -> Ubuntu VM
```

## iSCSI

The storage attachment path uses iSCSI.

Initiator IQN:

`iqn.1994-05.com.redhat:aa3e8af6d869`

The host tgt package and tgtd daemon were installed during troubleshooting.

## Troubleshooting

The Cinder API initially returned `/dev/vdb` when attaching the volume, but deeper verification showed that the attachment was rolled back.

The following layers were checked:

- Cinder volume state
- Cinder attachments
- Nova attachments
- LVM logical volume
- iSCSI configuration
- iscsid
- tgtd/tgtadm
- Docker containers
- Kolla-Ansible configuration
- Linux kernel modules
- configfs
- libvirt block devices
- Ubuntu guest lsblk

## LIO Investigation

The LIO target implementation was investigated using targetcli, lioadm, configfs and Linux target modules. The required iSCSI target fabric was not exposed correctly inside the Cinder container.

This demonstrated that containers share the host kernel but do not automatically have unrestricted access to every host kernel interface.

## Current Status

OpenStack, Nova, Neutron, Horizon, Glance and the Cinder LVM backend are functioning.

The 5 GB Cinder volume exists successfully as an LVM logical volume.

The remaining issue is the final Cinder-container-to-host-tgtd communication required for iSCSI attachment.

The volume has NOT been claimed as successfully attached until `/dev/vdb` is verified inside the Ubuntu VM.

## Key Lesson

A successful OpenStack API response is not proof that the infrastructure operation completed successfully.

Every layer must be verified:

```text
API
 -> OpenStack service
 -> Container
 -> Host
 -> Virtualization
 -> Guest
```

## Cloud Operations Mindset

```text
What changed?
 -> What is affected?
 -> Which layer is failing?
 -> What evidence proves it?
 -> Safest fix
 -> Verify
 -> Prevent recurrence
```

## Skills Learned

- Linux administration
- Docker
- OpenStack
- Kolla-Ansible
- Ansible
- Networking
- Open vSwitch
- KVM/QEMU/libvirt
- LVM
- Cinder
- iSCSI
- Troubleshooting
- Root-cause analysis
- Infrastructure automation

## Future Work

- Complete Cinder iSCSI attachment
- Verify /dev/vdb inside Ubuntu
- Practice failure recovery
- Add monitoring and logging
- Automate cloud operations with Ansible
- Learn Kubernetes
- Learn cloud security

## Senior-Level Summary

> I deployed a single-node OpenStack private cloud using Kolla-Ansible and Docker on BOSS GNU/Linux. I configured identity, compute, networking, images and block storage. I created an Ubuntu VM and investigated the complete Cinder LVM to iSCSI to Nova/libvirt storage attachment path. The most valuable part was troubleshooting the failure layer by layer across OpenStack, Docker, Linux, iSCSI, LVM and virtualization instead of assuming that an API response meant the operation had succeeded.
