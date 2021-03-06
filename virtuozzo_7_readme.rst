=======================
Virtuozzo 7 Beta Readme
=======================

Virtuozzo Overview
==================

Virtuozzo is a bare-metal virtualization solution that includes
container virtualization, KVM-based virtual machines, software-defined
storage along with enterprise features and production support. It runs
on top of Virtuozzo Linux, a RHEL-based Linux distribution.

The key changes in Virtuozzo 7 are as follows:

-  Virtuozzo 7 is based on RHEL 7 and Kernel 3.10+.

-  Virtuozzo 7 uses the KVM/QEMU hypervisor.

-  Containers use cgroups and namespaces that limit, account for, and
   isolate resource usage as isolated namespaces of a collection of
   processes. The beancounters interface remains in place for backward
   compatibility. At the same time, it acts as a proxy for actual
   cgroups and namespaces implementation.

-  UUIDs are used to identify both virtual machines and containers. With
   containers, ``prlctl`` treats the former VEID parameter as name.

-  VCMMD, the fourth-generation unified memory manager, and ``vcmmd``, a
   single daemon for managing memory of both virtual machines and
   containers. Virtuozzo 7 uses memcg. Balancing and configuring memcg
   limits enables getting the exact overcommit, shadow gangs, swap, page
   cache overuse Virtuozzo parameters.

What’s New in This Beta
=======================

This beta introduces virtual machines functionality, a new hypervisor, a
new virtual machine HDD image format, guest tools, a number of
improvements to the memory manager that benefit both containers and
virtual machines.

-  Virtuozzo 7 uses the KVM/QEMU hypervisor and enables customers to
   manage virtual machines via the libvirt API and VirtManager.

-  Virtual machine HDD images are stored in the QCOW2 format.

-  Virtuozzo provides guest tools for virtual machines that currently
   allow the following: to execute commands in VMs from the host, to set
   user passwords, to set and obtain network settings, to change SIDs,
   to enter VMs.

-  Autoballooning (technical preview). This beta features a technical
   preview of autoballooning for virtual machines. Before this release,
   overcommitted virtual machine memory was reclaimed by means of
   swapping. Now a much more efficient technique can be used: ``vcmmd``
   determines which virtual machines do not use their RAM actively and
   reclaims it by inflating a balloon inside every such VM.
   Autoballooning also provides benefits to mixed container/VM
   environments.

-  Memory guarantees. A memory guarantee is a percentage of container or
   virtual machine’s RAM that said container or VM is guaranteed to
   have. For virtual machines, the memory guarantee value is set to 40%
   by default; For containers, the default memory guarantee value is 0%.
   To change the default value, use the new
   ``prlctl set --memguarantee`` command.

-  Memory hotplugging. The new and improved memory hotplugging techique
   allows both increasing and reducing virtual machine RAM size on the
   fly, without the need to reboot the VM. Configured by the command
   ``prlctl --mem-hotplug``, memory hotplugging is implemented as a
   combination of ballooning and addition/removal of virtual DIMM slots.
   When a command to increase VM memory size is run, the memory is first
   expanded by deflating the VM’s balloon. If fully deflating the
   balloon is not enough to get the requested memory size, virtual DIMM
   slots are added. When a command to reduce VM memory size is run, the
   memory is shrunk by inflating the VM’s balloon. This feature is
   disabled by default and only supported for virtual machines with at
   least 1GB of RAM.

-  Kernel same-page merging. To optimize memory usage by virtual
   machines, Virtuozzo uses a feature of Linux called Kernel Same-Page
   Merging (KSM). The KSM daemon ``ksmd`` periodically scans memory for
   pages with identical content and merges those into a single page.
   Said page is marked as copy-on-write (COW), so when its contents are
   changed by a virtual machine, the kernel creates a new copy for that
   virtual machine. KSM enables the host to: avoid swapping due to
   merging of identical pages, run more virtual machines, overcommit
   virtual machine memory, and speed up RAM and hence certain
   applications and guest operating systems.

-  Cloning of virtual machines.

-  Renaming of virtual machines.

-  Setting of CPU affinity masks for virtual machines.

-  LVM support in prl\_disk\_tool. The prl\_disk\_tool can now
   resize/compact images with LVM.

-  In-place resizing of virtual machine disks.

-  Hotplugging of virtual machine disks.

Known Issues and Restrictions
=============================

-  Limited simfs support (feature provided as is). (OVZ-6581, OVZ-6613)
   Unlike OpenVZ, the simfs layout is based on bindmounts in Virtuozzo
   7. When a simfs-based container is started, its private area is
   bindmounted to the root container area. To create a simfs container:
   1) Set ``VEFSTYPE=simfs`` in ``/etc/vz/vz.conf``, 2) Run
   ``vzctl create ``. The simfs limitations in Virtuozzo 7 are: 1) No
   support for first- or second-level quotas, 2) No support for online
   migration of simfs-based containers.

-  Online and offline VM migration is supported as a proof of concept
   and may fail frequently. (PSBM-40084, PSBM-43767)

-  CPU limits for VMs work differently compared to Virtuozzo 6. Certain
   features like limits in MHz are not implemented. (PSBM-40823)

-  EFI virtual machines are not supported. (PSBM-40704)

-  In Windows VMs, each new SCSI HDD may be added as offline. As a
   workaround, you can either bring each disk back online manually.
   Alternatively, before the first boot with a new disk, run
   ``san policy=onlineall`` in DISKPART to set the SAN policy to
   ``OnlineAll``. (PSBM-41663)

-  Guest tools information may not be available for a VM with the guest
   tools installed until guest tools state is retrieved (e.g., by
   running ``prlctl list -i``). (PSBM-44235)

-  No connectivity between VM and external host on the same host-routed
   network IP subnet. (PSBM-44285)

-  Passwords with double quotes are not supported for virtual machines
   and containers. (PSBM-44576)

-  Host CD-ROM cannot be added to a VM. (PSBM-44773)

-  Private networks are not supported.

-  Basic Firewall is not supported.

-  Virtual Automation and Virtual Automation Agent are not supported.

-  Shared Folders functionality may not work.

Dropped Functionality
=====================

-  VZFS

-  Delayed ``/vz`` mounting

-  Commands ``prlctl --memquota``, ``prlsrvctl --mem-limit``.

-  The vzbackup, vzabackup, and pbackup utilities. For more information,
   see http://kb.virtuozzo.com/119017.

-  Migration of physical servers to Containers and migration of Xen
   virtual machines to virtual machines. For more information, see
   http://kb.virtuozzo.com/119016.

-  Start-As-User.

-  Shared smartcard (CCID) support.

-  The global iptables mask configured in ``/etc/sysconfig/vz``.

-  All UBC resources except ``physpages`` and ``swappages``.

-  The ``vziptables`` parameter configured in the kickstart file used
   for unattended installations of Virtuozzo.
