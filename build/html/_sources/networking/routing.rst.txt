========
routing
========

How to enable routing permanently
------------------------------------

This file shows if routing is currently enabled on the system (1=on)::

   /proc/sys/net/ipv4/ip_forward

To make this change permanent upon reboots change file **/etc/sysctl.conf** to::

   net.ipv4.ip_forward=1

To implement the changes immediately on the local system, run the following command::

   # sysctl -p