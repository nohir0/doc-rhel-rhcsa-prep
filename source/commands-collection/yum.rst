====
yum
====


Which packages are installed within the **base** package group?
-------------------------------------------------------------------

Use yum group info::

   # yum group info base

**Hint:** On the RHEL installation media there is a XML file called **/repodata/*-comps-Server-x86_64.xml** where <grouplist> and <optionlist> shows the belonging package information


.. _yum-mount-iso-dvd:

How to setup a local yum repository for locally mounted ISO
----------------------------------------------------------------

https://access.redhat.com/solutions/1355683

Issue
   * How to set up yum repository to use locally-mounted DVD with Red Hat Enterprise Linux (RHEL) 7
   * Would like to upgrade server from RHEL 7.x to RHEL 7.y
   * Have a secure environment that will never be connected to the internet, but still needs to be updated
   * Way to update the packages on server, with no satellite server and servers disconnected from internet
   * Offline patches for Red Hat systems
   * How do I create a local repository in RHEL 7?

1. Mount the RHEL 7 installation ISO to a directory like /mnt::

   # mount -o loop RHEL7.1.iso /mnt


2. Copy the media.repo file from the root of the mounted directory to /etc/yum.repos.d/ and set the permissions to something sane::

   # cp /mnt/media.repo /etc/yum.repos.d/rhel7dvd.repo
   # chmod 644 /etc/yum.repos.d/rhel7dvd.repo


3. Edit the new repo file changing the gpgcheck=0 setting to 1 and adding the following 3 lines:
::

   enabled=1 
   baseurl=file:///mnt/ 
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release 


In the end, the new repo file could look like the following::

   [InstallMedia]
   name=DVD for Red Hat Enterprise Linux 7.1 Server
   mediaid=1359576196.686790
   metadata_expire=-1
   gpgcheck=1
   cost=500
   enabled=1
   baseurl=file:///mnt/
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release


4. clear the related caches by yum clean all and subscription-manager clean once::

   # yum clean all
   # subscription-manager clean


5. check whether you can get the packages list from the DVD repo::

   # yum  --noplugins list


6. if no problem , you wil update::

   # yum  --noplugins update




How to setup a local yum repository for locally mounted CDROM/DVD
-------------------------------------------------------------------

http://www.itzgeek.com/how-tos/linux/centos-how-tos/create-local-yum-repository-on-centos-7-rhel-7-using-dvd.html

1. In case of a CDROM, mount the device (cdrom must be connected in esxi)::

   # mkdir /cdrom
   # mount /dev/cdrom /cdrom


2. Before creating a repo file, move the existing repo files present in /etc/yum.repos.d directory, if not required:
::

   mkdir /etc/yum.repos.d/original
   mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/original


3. Create the new repo file called cdrom.repo under /etc/repos.d directory::

   # vi /etc/yum.repos.d/local.repo


Enter follwing details::

   [LocalRepo]
   name=LocalRepository
   baseurl=file:///cdrom
   enabled=1
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7


4. Clear the repository cache by issuing the following command::

   # yum clean all



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
