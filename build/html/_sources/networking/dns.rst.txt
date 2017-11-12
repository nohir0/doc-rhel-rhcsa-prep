###
dns
###

HEL 7 includes at least four hostname configuration files of interest: /etc/hostname,
/etc/hosts, /etc/resolv.conf, and /etc/nsswitch.conf

/etc/nsswitch.conf
-------------------
This file defines the order of search for network name entries
::

   hosts: files dns


Hostname
-----------

Change hostname permanently
::

   # hostnamectl set-hostname newname