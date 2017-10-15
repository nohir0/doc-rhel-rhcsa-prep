======
mount
======


Which formats are supported on the system::

   [root@centos_server1 ~]# cat /etc/filesystems
    xfs
    ext4
    ext3
    ext2
    nodev proc
    nodev devpts
    iso9660
    vfat
    hfs
    hfsplus
    *

Mount ISO File::

   # mount -o loop rhel-server-7.0-x86_64-dvd.iso /media

Technically a loop device is a block device that writes to a file, rather than a piece of hardware. So you always use/need to use the loop back device when mounting a file.
