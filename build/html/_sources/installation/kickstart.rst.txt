kickstart
===========

Two methods for creating the kickstart configuration file:
   * /root/anaconda-ks.cfg
   * graphical kickstart configurator **system-config-kickstart** command

::

   # yum install system-config-kickstart

There is also software acting as installation server for greater deployments like:
http://cobbler.github.io/


How to start installation with kickstart file
-----------------------------------------------

   1. Mount filesystem with kickstart file
   2. On the first menu of RHEL installation press <TAB>
   3. Add the "ks" option to the vmlinz parameter line

::

   > vmlinuz initrd=initrd.img inst.stage2=hd:LABEL=RHEL-7.0 \x20Server.x86_64 quiet ks=hd:sdb1:/ks.cfg

It is possible to fetch the kickstart file from network too

::

   ks=nfs:192.168.122.1:/ks.cfg
   ks=http://192.168.122.1/ks.cfg



Important kickstart file options
------------------------------------

After kickstart configuration file creation a validation should be made via **ksvalidator** command.

::

   # version=RHEL7
   # authconfig --enableshadow --passalgo=sha512


Installation source:
::

   # cdrom
   # nfs --server=192.168.122.1 --dir=/inst
   # url --url http://192.168.122.1/inst
   # url --url ftp://192.168.122.1/pub/inst

   # harddrive --partition=/dev/sda10 --dir=/tmp/michael/

   # firstboot --disabled

   # keyboard --vckeymap=us --xlayouts='us'
   # lang en_US.UTF-8

   # rootpw --iscrypted $6$5UrLfXTk$CsCW0nQytrUuvycuLT317/
   # user --groups=wheel name=michael --password=... --iscrypted --gecos="MJ"

  # timezone America/Los_Angeles --isUtc

The network configuration must be **one line**!
::

   # network --device eth0 --bootproto dhcp
   # network --bootproto static --device=eth0 --gateway=192.168.122.1 --ip=192.168.122.150 --netmask=255.255.255.0 --noipv6 --nameserver==192.168.122.1 --activate network --hostname tester1.example.com

   # firewall --service=ssh
   # selinux --enforcing

Boot storage and partitions
Remove any --onpart directives, otherwise an error occurs during installation
::

   # bootloader --location=mbr --boot-drive=vda
   # clearpart --all --initlabel –-drives=vda

   # part /boot --fstype="xfs" --size=500
   # part swap --fstype="swap" --size=1000
   # part / --fstype="xfs" --size=10000
   # part /home --fstype="xfs" --size=1000

   # part pv.01 --fstype="lvmpv" --ondisk=vda --size=11008
   # part /boot --fstype="xfs" --ondisk=vda --size=500
   # part swap --fstype="swap" --ondisk=vda --size=1000
   # volgroup rhel --pesize=4096 pv.01
   # logvol / --fstype="xfs" --size=10000 --name=root --vgname=rhel
   # logvol /home --fstype="xfs" --size=1000 -name=home --vgname=rhel

   #repo --name="Red Hat Enterprise Linux" --baseurl=ftp://192.168.122.1/pub/inst --cost=100

Package installation:
::

   # %packages
   # @base
   # @core
   # ...
   # @print-client
   # @x11
   # %end

   # %post
   # ...

