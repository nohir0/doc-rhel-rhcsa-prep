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



Important Files and directories
---------------------------------

   * KVM Konfiguration Files: **/etc/libvirt/qemu**
   * Disk images: **/var/lib/libvirt/images**

Changes in the XML config files are implemented only after **libvirtd** service restart!
::

   # systemctl restart libvirtd


Important commands
-------------------

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