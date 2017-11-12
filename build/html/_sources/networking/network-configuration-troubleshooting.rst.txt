#####################################
network-configuration-troubleshooting
#####################################


Red Hat stores network information in the directory **/etc/sysconfig/network-scripts** and in **/etc/sysconfig/network**.
Example file of **/etc/sysconfig/network-scripts/ifcfg-ens192**

::

   TYPE="Ethernet"
   PROXY_METHOD="none"
   BROWSER_ONLY="no"
   BOOTPROTO="none"
   DEFROUTE="yes"
   IPV4_FAILURE_FATAL="no"
   IPV6INIT="yes"
   IPV6_AUTOCONF="yes"
   IPV6_DEFROUTE="yes"
   IPV6_FAILURE_FATAL="no"
   IPV6_ADDR_GEN_MODE="stable-privacy"
   NAME="ens192"
   UUID="25946d9c-b79c-415d-97e0-638bc017c62d"
   DEVICE="ens192"
   ONBOOT="yes"
   IPADDR="192.168.122.100"
   PREFIX="24"
   GATEWAY="192.168.122.254"
   DNS1="192.168.0.237"
   DNS2="8.8.8.8"
   IPV6_PRIVACY="no"
   ZONE=public


After changes in ifcfg files, they are applied via Network Manager command
::

   # nmcli con reload
   # nmcli con down eth0
   # nmcli con up eth0


Check the status of network service
::

   # systemctl status network

Restart networking
::

   # systemctl restart network


Network Manager status check
::

   # systemctl status NetworkManager

Network Manager CLI
::

   # nmcli dev status



Configuring VLAN Trunk With Sub-Interfaces
--------------------------------------------
First thing is to make sure that the 801.1q kernel module is loaded:
::

   # lsmod | grep 8021q

If not, load it and make it persistent:
::

   # modprobe 8021q
   # echo "8021q" > /etc/modules-load.d/8021q.conf

The physical interface is configured in **/etc/sysconfig/network-scripts/ifcfg-***
::

   DEVICE=eth0
   TYPE=Ethernet
   BOOTPROTO=none
   ONBOOT=yes


Configure the VLAN interface script in /etc/sysconfig/network-scripts. The configuration filename must be the physical interface plus a “.” character plus the VLAN id number. For example, if the VLAN id is 10, and the physical network interface is eth0, then the configuration filename should be ifcfg-eth0.10, as the example below:
::

   DEVICE=eth0.10
   BOOTPROTO=none
   ONBOOT=yes
   IPADDR=14.1.1.31
   NETMASK=255.255.255.0
   USERCTL=no
   NETWORK=14.1.1.0
   VLAN=yes

::

   # service network restart



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
