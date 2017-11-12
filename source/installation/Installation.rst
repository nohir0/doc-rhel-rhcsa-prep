====================
CentOs Installation
====================

Force Text Setup
-----------------

You can also force installation in text mode if you wish. To do so, highlight the Install Red
Hat Enterprise Linux 7.0 option and press the tab key. When you do, the following options
are revealed on that screen, on one line::

	> vmlinuz initrd=initrd.img inst.stage2=hd:LABEL=RHEL-7.0\x20Server.x86_64 quiet

To force installation in text mode, add **inst.text** to the end of this line.


How to setup a local yum repository for locally mounted DVD/ISO
----------------------------------------------------------------

Documented under commands-collection yum: :ref:`yum-mount-iso-dvd`.


How to install Display Manager (GNOME) after initial (minimal) Installation
----------------------------------------------------------------------------

::

   # yum group install "Server with GUI"
   # systemctl set-default graphical.target
     Removed symlink /etc/systemd/system/default.target.
     Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/graphical.target.



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
