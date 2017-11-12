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



Default Route Configuration
----------------------------
::
   # cat /etc/sysconfig/network
   NETWORKING=yes
   HOSTNAME=testing.example.com
   GATEWAY=10.1.6.1



Static Routes
--------------
::

   # ip route show
   default via 192.168.122.254 dev ens192 proto static metric 100 
   192.168.122.0/24 dev ens192 proto kernel scope link src 192.168.122.100 metric 100 
   192.168.123.0/24 dev virbr2 proto kernel scope link src 192.168.123.1 
   192.168.124.0/24 dev virbr0 proto kernel scope link src 192.168.124.1

::

   # ip route add 10.1.5.202/32 via 10.1.7.1 dev eth1
   # ip route add 10.1.5.0/24 via 10.1.7.1 dev eth1

To make static routes permanent:
::

   # vi /etc/sysconfig/network-scripts/route-eth1

and append e.g.:
::

   10.1.5.0/24 via 10.1.7.1 dev eth1



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
