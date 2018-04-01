#################
network-commands
#################


ifconfig | arp | netstat | route are "obsolete"
-------------------------------------------------

*ifconfig*

::

   # ip link show
   # ip addr show
   # ip -s link

   # ip addr add 192.168.122.150/24 dev eth0
   # ip link set dev eth0 up|down
   # ip link set dev device promisc on|off
   # ip link set dev device mtu N


*arp and route*

::

   # ip neigh show
   # ip route

*netstat*

::

   # ss -tuna4


Configure | Manipulate Network Adapter
-----------------------------------------

::

   ip addr add 192.168.122.150/24 dev eth0
   ip addr flush dev eth0
   ip link set dev eth0 up | down
   ip link set dev eth0 promisc on | off



Network Manager
----------------

   * **nmcli** command line tool
   * **nmtui** text based graphical tool
   * **nm-connection-editor** GTK3 based tool

Check if it is running

::

   systemctl status NetworkManager


::

   # nmcli con show
   NAME    UUID                                  TYPE            DEVICE 
   ens192  25946d9c-b79c-415d-97e0-638bc017c62d  802-3-ethernet  ens192 
   virbr0  c9f5d0ca-a518-436c-adea-6db1c243c71f  bridge          virbr0 
   virbr1  8d8c5bff-2558-4ceb-b99a-e1aa14a120f5  bridge          virbr1 
   vnet0   605fe99d-1d3b-4242-8127-d172c7450def  tun             vnet0  

   # nmcli con add con-name "eth0-work" type ethernet ifname eth0
   # nmcli con mod "eth0-work" ipv4.addresses "192.168.20.100/24 192.168.20.1"
   # nmcli con mod "eth0-work" +ipv4.dns 192.168.20.1


Notify NetworkManager about changes in config files
::

   nmcli con reload
   nmcli con down eth0
   nmcli con up eth0

Switch to the new connection profile
::

   nmcli con up "eth0-work"
   nmcli dev status

Prevent Autostart
::

   # nmcli con mod "eth0-work" connection.autoconnect no


Graphical Networking User Interfaces
-------------------------------------

   * nmtui
   * nm-connection-editor


Network Connection Directives
-----------------------------

These directives can be configured in interface scripts

.. image:: images/rhel_network-connection-directives.png



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
