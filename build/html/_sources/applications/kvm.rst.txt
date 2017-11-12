========
kvm
========

.. _kvm-application:


Mounting installation media for VM installation
-------------------------------------------------
This is acutally not a simple task in RH|CentOS. 
   * CDROM mount passthrough from host to guest is not supported on my system at least...
   * Mounting an ISO image brings SELinux issues, qemu cannot access these directories initialls, additional configuration is necessary
   * I found the easiest approach is to access the install media via network protocols directly
   * In my first successful test I installed vsftpd and copied the CentOS DVD data to /var/ftp/pub/centos and accessed it via virtualization-manager
   * It is most probably a SELinux issue, found a solution for "Permission denied" when mounting .iso images
      * Had to use one command that allowed me to mount .iso e.g. located in /home/gans/KVM/install: **setsebool -P virt_use_nfs 1**
      * Maybe I also had to use virt-manager as root, but not sure about that



Important Files and directories
---------------------------------

   * KVM Konfiguration Files: **/etc/libvirt/qemu**
   * Disk images: **/var/lib/libvirt/images**

Changes in the XML config files are implemented only after **libvirtd** service restart!
::

   # systemctl restart libvirtd


Important commands
-------------------

   * **virt-manager**
   * **virt-install**
   * **virsh**
   * **virt-clone**


virt-install
-------------

::

   # virt-install --name test.example.com \
   > --ram=1024 --vcpus=2 \
   > --disk path=/var/lib/libvirt/images/test.example.com,size=16 \
   > --graphics=spice \
   > --location=ftp://192.168.122.100/pub/inst \
   > --os-type=linux \
   > --os-variant=rhel7

If a mistake happens during VM creation, Ctrl+C aborts the process,
but the VM will be still running and the same name cannot be reused,
because there is a configuration file and virtual disk. 
To get rid of this wrong VM....


Delete a VM from command line
------------------------------

1. Stop the VM::

   # virsh destroy test.example.com

2. Delete associated XML configuration file in **/etc/libvirt/qemu** and virtual disk file in **/var/lib/libvirt/images** (not if it is to be reused)
::

   # virsh undefine test.example.com --remove-all-storage

3. Now **virt-install** can be run again with same name


virsh
------
VM management from the command line

  * Start VM
  * Stop VM
  * Delete VM

::

   # virsh list --all
   # virsh capabilities
   # virsh start server1.example.com
   # virsh shutdown server1.example.com
   # virsh destroy server1.example.com
   # virsh autostart server1.example.com
   # virsh autostart --disable tester1.example.com



virt-clone
-----------
System to be cloned must be shutdown first

::

   # virt-clone --original=server1.example.com \
   > --name=tester1.example.com \
   > --file=/var/lib/libvirt/images/tester1.example.com.img \
   > --file=/var/lib/libvirt/images/tester1.example.com-1.img \
   > --file=/var/lib/libvirt/images/tester1.example.com-2.img

IP addressing and also MAC address seems to be same like in the original image.
To scale that kind of additional changes, the **kickstart** feature can be used.