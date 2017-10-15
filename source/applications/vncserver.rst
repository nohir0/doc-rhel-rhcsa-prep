==========
vncserver
==========

VNC Server Installation auf CentOS 7::

   https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-tigervnc
   https://www.howtoforge.com/vnc-server-installation-on-centos-7


Install the vnc server::

   yum install tigervnc-server

**@:1** stands for tcp port 5901::

   cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
   vi /etc/systemd/system/vncserver@:1.service

Replace <USER> with the actual user whose desktop should be accessed::

   [...]
   [Service]
   Type=forking
   # Clean any existing files in /tmp/.X11-unix environment
   ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
   #ExecStart=/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
   #PIDFile=/home/<USER>/.vnc/%H%i.pid
   ExecStart=/sbin/runuser -l srijan -c "/usr/bin/vncserver %i"
   PIDFile=/home/srijan/.vnc/%H%i.pid
   ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'

Adopt firewall rules to allow remote access::

   firewall-cmd --permanent --zone=public --add-service vnc-server
   firewall-cmd --reload

Create password for the user (this seems to be saved in cleartext on the system?!)
This also starts the server::

   su - user
   vncserver :1

Make the service enabled on after every reboot with root credentials
**But this destroys the vncserver somehow, could not find out why yet...**
So until now I start the vncserver manually with above command after reboots::

   su -
   systemctl daemon-reload

   systemctl enable vncserver@:1.service
   reboot
   systemctl start vncserver@:1.service