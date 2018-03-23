#################
Kernel Topics
#################


Verify And Load Kernel Modules
-------------------------------
::

   # lsmod | grep 8021q
   #
   # modprobe 8021q
   #
   # lsmod | grep 8021q
   8021q                  33159  0 
   garp                   14384  1 8021q
   mrp                    18542  1 8021q



Load Kernel Module Persistent
-------------------------------
::

   # echo "8021q" > /etc/modules-load.d/8021q.conf
   # cat /etc/modules-load.d/8021q.conf 
   8021q