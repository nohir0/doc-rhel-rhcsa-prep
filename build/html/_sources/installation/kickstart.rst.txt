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

   # --> you can also point the install source to an ISO file:
   # harddrive --partition=/dev/sda10 --dir=/tmp/michael/

   # firstboot --disabled

   # keyboard --vckeymap=us --xlayouts='us'
   # lang en_US.UTF-8

   # --> you can use the password from /etc/shadow because it uses same encryption
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
What follows is a list of package groups that are installed through this Kickstart configuration file.
These names correspond to the names you can find in the \*-comps-Server.x86_64.xml file in the RHEL 7 DVD /repodata directory described in Chapter 1.
Because the list is long, the following is just an excerpt of package groups (which startwith @) and package names

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



Working example for KVM based kickstart installation
-----------------------------------------------------

It is important to have an ISO file as CentOS/RHEL source (cdrom passthrough to KVM VM is currently disabled by RH!),
this is necessary to see the installation screen, it allows to alter the boot parameters (by pressing TAB)
Second thing is to use "cdrom" as installation media for KVM installation, because network based installation is more complicated.

::

   [root@kvm-server01 ~]# cat /var/www/html/inst/ks.cfg 
   #version=DEVEL
   # License agreement
   eula --agreed
   # System authorization information
   auth --enableshadow --passalgo=sha512
   
   # Use CDROM installation media
   cdrom
   # url --url="http://192.168.122.1/inst"
   
   # Use graphical install
   graphical
   
   # Run the Setup Agent on first boot
   # firstboot --enable
   firstboot --disabled
   
   # System services
   services --disabled="chronyd"
   
   # Keyboard layouts
   keyboard --vckeymap=at --xlayouts='at'
   
   # System language
   lang en_US.UTF-8
   
   # ignoredisk --only-use=sda
   
   # Network information
   network  --bootproto=static --device=eth0 --gateway=192.168.100.1 --ip=192.168.100.100 --nameserver=8.8.8.8,192.168.0.237    --netmask=255.255.255.0 --ipv6=auto --activate   
   network  --hostname=outsider01.example.com   
   
   # Root password
   rootpw --iscrypted $6$GfLyBLABLABLA
   
   # System timezone
   timezone Europe/Vienna --isUtc --nontp
   
   user --name=user1 --password=$6$FlVBBLABLABLA    --iscrypted --gecos="user1"   
   
   firewall --service=ssh
   selinux --enforcing
   
   # System bootloader configuration
   bootloader --append=" crashkernel=auto" --location=mbr --boot-drive=vda
   
   # Partition clearing information
   clearpart --all --initlabel --drives=vda
   
   # Disk partitioning information
   part pv.01 --fstype="lvmpv" --ondisk=vda --size=13523
   part /boot --fstype="xfs" --ondisk=vda --size=953
   part swap --fstype="swap" --ondisk=vda --size=1907
   volgroup centos --pesize=4096 pv.01
   logvol /  --fstype="xfs" --size=9536 --name=root --vgname=centos
   logvol /home  --fstype="xfs" --size=3979 --name=home --vgname=centos
   
   # repo --name=myrepo --baseurl=http://192.168.122.1/inst
   
   shutdown
   
   %packages
   @base
   @core
   %end
   
   %addon com_redhat_kdump --enable --reserve-mb=auto
   %end
   
   %anaconda
   pwpolicy root --minlen=6 --minquality=1 --notstrict --nochanges --notempty
   pwpolicy user --minlen=6 --minquality=1 --notstrict --nochanges --emptyok
   pwpolicy luks --minlen=6 --minquality=1 --notstrict --nochanges --notempty
   %end




Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
