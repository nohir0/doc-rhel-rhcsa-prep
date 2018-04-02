############
selinux
############

Basic Features of SELinux
-----------------------------

The SELinux security model is based on subjects, objects, and actions.
   * A subject is a process, such as a running command or an application such as the Apache web server in operation. 
   * An object is a file, a device, a socket, or in general any resource that can be accessed by a subject.
   * An action is what may be done by the subject to the object.

SELinux assigns different **contexts** to objects. A context is just a label, which is used by
the SELinux security policy to determine whether a subject’s action on an object is allowed
or not.

To see the context of a particular file, run the **ls -Z** command.

SELinux Status
----------------

As suggested in the RHCSA objectives, you need to know how to “set enforcing and
permissive modes for SELinux.” There are three available modes for SELinux: **enforcing, permissive, and disabled**. 
The enforcing and disabled modes are self-explanatory.

::

   ls -Z
   drwxr-xr-x. root root unconfined_u:object_r:user_home_t:s0 acltest
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Desktop
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Documents
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Downloads
   drwxr-xr-x. gans gans system_u:object_r:audio_home_t:s0 Music
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Pictures
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Public
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Templates
   drwxr-xr-x. gans gans system_u:object_r:user_home_t:s0 Videos


Equivalent commands for processes, network connections

::

   ps -Zaux
   ss -Z
   netstat -Z


SELinux in **permissive** mode means that any SELinux rules that are violated are logged, but the violation does not stop any action.
If you want to change the default SELinux mode, change the SELINUX directive in the **/etc/selinux/config** file.
The next time you reboot, the changes are applied to the system.

If SELinux is configured in enforcing mode, it protects systems in one of two ways: in
targeted mode or in mls mode. The default is the targeted policy, which allows you to
customize what is protected by SELinux in a fine-grained manner. In contrast, MLS goes a
step further, using the Bell-La Padula model developed for the US Department of Defense.

If you just want to experiment with SELinux, configure it in permissive mode. It’ll
log any violations without stopping anything. It’s easy to set up with the SELinux
Administration tool, or you can set SELINUX=permissive in /etc/selinux/config. If
the auditd service is running, violations are logged in the audit.log file in the
/var/log/audit directory. Just remember, it’s likely that Red Hat wants candidates
to configure SELinux in enforcing mode during the exams.


Basic SELINUX Configuration and Settings
-------------------------------------------
::

   getenforce
   Enforcing

::

   sestatus
   SELinux status:                 enabled
   SELinuxfs mount:                /sys/fs/selinux
   SELinux root directory:         /etc/selinux
   Loaded policy name:             targeted
   Current mode:                   enforcing
   Mode from config file:          enforcing
   Policy MLS status:              enabled
   Policy deny_unknown status:     allowed
   Max kernel policy version:      28

You can change the current SELinux status with the setenforce command; the options
are straightforward:

::

   setenforce enforcing
   setenforce permissive

To make this change permanent, you’ll have to modify the SELINUX variable in the **/etc/selinux/config** file.


Setting Context Types (Essentials for RHCSA !)
-----------------------------------------------

There are two commands to set context type:
   * semanage - This is the command you want to use
   * chcon - Use it only in specific cases - **should normally be avoided** - does not survive file system is relabeled !

To set context using semanage, you need to find out the correct context.
The easy way is to look for default context settings for alreasy existing items (e.g. files / directories)

::

   ls -Z /var/www
   drwxr-xr-x. root root system_u:object_r:httpd_sys_script_exec_t:s0 cgi-bin
   drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 html

To set this type for html directory to any new directory that should be accessible by the Apache web server,
use the following command:

::

   semanage fcontext -a -t httpd_sys_content_t "/mydir(/.*)?"

The option **-a** is used to add a context type.
The option **-t** is used to change a context type (as opposed to user and role)

Setting the context this way is not enough, because it writes it only to the policy and not to the file system.
To complete the task, you need to apply the policy to the file system:

::

   restorecon -R -v /mydir

It is not easy to remember the **semanage fcontext** command.
**There is a good man page for it - man semanage-fcontext - with useful examples at the bottom !**


Finding the Context Type You Need
----------------------------------

This is one of the challenging parts of setting SELINUX contexts.
Roughly, there are three approaches:

   * Look at the default environment
   * Read the configuration files
   * Use **man -k _selinux** to find SELinux-specific man pages for your service

The **man -k _selinux** pages are not installed by default.
To install them you need the package **policycoreutils-devel** and then another task:

::

   sepolicy manpage -a -p /usr/share/man/man8

This is an essential skill - you should master it before going to the exam:

   1. **man -k _selinux** - it will only provide one or two man pages
   2. **yum whatprovides \*/sepolicy**
   3. **yum install policycoreutils-devel**
   4. **sepolicy manpage -a -p /usr/share/man/man8**
   5. **man -k _selinux** - you'll see no changes
   6. **mandb**
   7. This takes some time, after that a lot more man pages should appear with the man command
   8. **man -k _selinux | grep http** as example for finding the correct context


Configure Regular Users for SELinux (not needed for RHCSA !)
--------------------------------------------------------------
To review the status of current SELinux users, run the semanage login -l command

::

   semanage login -l
   
   Login Name           SELinux User         MLS/MCS Range        Service
   
   __default__          unconfined_u         s0-s0:c0.c1023       *
   root                 unconfined_u         s0-s0:c0.c1023       *
   system_u             system_u             s0-s0:c0.c1023       *

In other words, regular “default” users have the same SELinux user context of the
root administrative user. To confirm, run the id -Z command as a regular user. Without
changes, it leads to the following output, which suggests that the user is not confined by any
SELinux settings:

::

   unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

In other words, regular “default” users have the same SELinux user context of the
root administrative user. To confirm, run the id -Z command as a regular user.

The preceding string defines what is called a label in SELinux jargon. A label is made up
of several context strings, separated by a column: a user context (which ends with a _u), a
role context (which ends with _r), a type context (which ends with _t), a sensitivity context,
and a category set. The rules of the targeted policy, which is the default SELinux policy in
RHEL 7, are mostly associated with the type (_t) context.

Although it may not be an exam requirement, regular users should be confined by
SELinux. When user accounts are compromised, and they will be compromised, you want
any damage that might be caused limited by SELinux rules. The following example specifies
a confinement rule that adds (-a) regular user michael, specifying (-s) the user_u context for
confinement:

::

   semanage login -a -s user_u michael

f desired, you can reverse the process with the **semanage -d michael** command.

When a user role is changed, it doesn’t take effect until the next login. For example, if
we were to change the role for user michael to user_u in a GUI-based command line, the
change would not take effect until we logged out and logged back in to the GUI. If you
were to try this on your system, you would no longer be able to start any administrative
configuration tools, and you would not have access to the sudo and su commands.
On some networks, you may want to change the role of future users to user_u. If you
don’t want regular users tinkering with administrative tools, you could make that change for
future default users with the following command:

::

   semanage login -m -S targeted -s "user_u" -r s0 __default__

This command modifies (-m) the targeted policy store (-S), with SELinux user (-s)
user_u, with the MLS s0 range (-r) for the default user. Here, “__default__” includes two
underscore characters on each side of the word. As long as user_u is in effect for the default
SELinux user, regular users won’t have access to use administrative tools or commands such
as su and sudo. The following command reverses the process:

::

   semanage login -m -S targeted -s "unconfined_u" \
   -r s0-s0:c0.c1023 __default__


.. image:: images/rhel_selinux_user_roles.png


Manage SELinux Boolean Settings
-------------------------------

Most SELinux settings are boolean- in other words, they’re activated and deactivated by
setting them to 1 or 0, respectively. Once set, the booleans can be retrieved from the
/sys/fs/selinux/booleans directory. One simple example is selinuxuser_ping, which is
normally set to 1, which allows users to run the ping and traceroute commands.

These settings can be read with the **getsebool -a** and modified with the **setsebool** commands.
For example, the following output from the getsebool user_exec_content command confirms
that SELinux allows users to execute scripts either in their home directories or from the /tmp
directory:

::

   user_exec_content --> on

For example, the following command disables the noted boolean until the system is rebooted:

::

   setsebool user_exec_content off

A full list of available booleans is available in the output to the **getsebool -a** command.
For more information on each boolean, run the **semanage boolean -l** command.

::

   getsebool -a
   semanage boolean -l



List and Identify SELinux File Contexts
----------------------------------------

If you’ve enabled SELinux, the ls -Z command lists current SELinux file contexts, as shown
earlier in Figure 4-10. As an example, take the relevant output for the anaconda-ks.cfg file
from the /root directory:

::

   -rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg

It specifies four elements of SELinux security: **the user, role, type, and MLS level** for the noted file.
Generally, the SELinux user associated with a file is system_u or unconfined_u, and this
generally does not affect access. In most cases, files are associated with an object_r, an
object role for the file. It’s certainly possible that future versions of the SELinux targeted
policy will include more fine-grained options for the user and role.

The key file context is the type, in this case, **admin_home_t**. When you configured FTP
and HTTP servers in Chapter 1, you changed the type of the configured directory and the
files therein to match the default type of shared files from those services with the chcon
command.

For example, to configure a nonstandard directory for an FTP server, make sure the
context matches the default FTP directory. Consider the following command:

::

   ls -Z /var/ftp/
   drwxr-xr-x. root root system_u:object_r:public_content_t pub

If you create an /ftp directory as the root user and run the ls -Zd /ftp command, 
you’ll see the contexts associated with the /ftp directory as shown:

::

   drwxr-xr-x. root root unconfined_u:object_r:root_t /ftp

To change the context, use the chcon command. If there are subdirectories, you’ll want
to make sure changes are made recursively with the -R switch. In this case, to change the
user and type contexts to match /var/ftp, run the following command:

::

   chcon -R -u system_u -t public_content_t /ftp

If you want to support uploads to your FTP server, you’ll have to assign a different type
context, specifically public_content_rw_t. That corresponds to the following command:

::

   chcon -R -u system_u -t public_content_rw_t /ftp

In Chapter 1, you used a different variation on the chcon command. To use that lesson,
the following command uses user, role, and context from the /var/ftp directory and applies
the changes recursively:

::

   chcon -R --reference /var/ftp /ftp


Using **restorecon** is the preferred way to change file contexts because it sets the
contexts to the values configured in the SELinux policy. The **chcon** command can
modify file contexts to any value passed as an argument, but the change may not
survive a filesystem relabeling if a context differs from the default value defined
in the SELinux policy.
Hence, to avoid mistakes, you should modify contexts in the SELinux policy with **semanage fcontext** 
and use **restorecon** to change file contexts.


Restore SELinux File Contexts
-------------------------------

How are context settings applied?
   * If a new file is created, it inherits the context settings from the parent directory
   * If a file is copied to a directory, this is considered a new file, so it inherits the context settings from the parent directory
   * If a file is moved, or copied while keeping its properties (by using **cp -a**), the original file context settings of the file are applied

Especially the latter of these three situations is easily fixed by using restorecon.
It is also possible to relabel the entire file system - this could be a good idea.

::

   restorecon -Rv /


Default contexts are configured in /etc/selinux/targeted/contexts/files/file_contexts. If you
make a mistake and want to restore the original SELinux settings for a file, the restorecon
command restores those settings based on the file_contexts configuration file. However, the
defaults in a directory may vary. For example, the following command (with the -F switch
forcing a change to all contexts rather than just the type context) leads to a different set of
contexts for the /ftp directory:

::

   # restorecon -F /ftp
   # ls -Zd /ftp
   drwxr-xr-x. root root system_u:object_r:default_t ftp

You may also list all default file contexts rules in file_contexts with the **semanage fcontext -l** command.

As an example, a regular expression that matches the /ftp directory and all files in it is
given by the following:

::

   /ftp(/.*)?

Using this regular expression, we can define a SELinux policy rule that assigns to the
/ftp directory and all files in it a default type context. This can be done with the **semanage fcontext -a** command. 
For example, the following command assigns a default type context
of public_content_t to the /ftp directory and all the files in it:

::

   semanage fcontext -a -t public_content_t '/ftp(/.*)?'

Once you have defined a new default policy context for a filesystem path, you can run
the restorecon command to set the contexts to the corresponding default policy values.

The following command restores the context recursively (-R) to the public_content_t value defined previously:

::

   restorecon -RF /ftp
   ls -Zd /ftp
   drwxr-xr-x. root root system_u:object_r:public_content_t ftp


Identify SELinux Process Contexts
---------------------------------

The ps command lists currently running processes. 
In a SELinux system, there are contexts for each running process. 
To see those contexts for all processes currently in operation, run the **ps -eZ** command, which lists every (-e) process SELinux
context (-Z).

::
   ps -eZ
   system_u:system_r:irqbalance_t:s0 701 ?        00:00:13 irqbalance
   system_u:system_r:syslogd_t:s0    705 ?        00:00:13 rsyslogd
   system_u:system_r:alsa_t:s0       707 ?        00:00:00 alsactl
   system_u:system_r:policykit_t:s0  708 ?        00:00:30 polkitd
   system_u:system_r:abrt_t:s0-s0:c0.c1023 710 ?  00:00:00 abrtd
   system_u:system_r:abrt_watch_log_t:s0 712 ?    00:00:00 abrt-watch-log
   system_u:system_r:modemmanager_t:s0 713 ?      00:00:00 ModemManager
   system_u:system_r:fsdaemon_t:s0   716 ?        00:00:00 smartd
   system_u:system_r:rtkit_daemon_t:s0 720 ?      00:00:02 rtkit-daemon
   system_u:system_r:systemd_logind_t:s0 722 ?    00:00:07 systemd-logind
   system_u:system_r:lsmd_t:s0       726 ?        00:00:00 lsmd
   system_u:system_r:avahi_t:s0      727 ?        00:00:00 avahi-daemon
   system_u:system_r:abrt_watch_log_t:s0 728 ?    00:00:00 abrt-watch-log
   system_u:system_r:system_dbusd_t:s0-s0:c0.c1023 731 ? 00:00:30 dbus-daemon
   system_u:system_r:avahi_t:s0      740 ?        00:00:00 avahi-daemon
   system_u:system_r:chronyd_t:s0    742 ?        00:00:01 chronyd


Diagnose and Address SELinux Policy Violations
-------------------------------------------------

If there’s a problem, SELinux is running in enforcing mode, and you’re sure there are no
problems with the target service or application, don’t disable SELinux! Red Hat has made it
easier to manage and troubleshoot. According to Red Hat, the top two causes of SELinux-
related problems are contexts and boolean settings.

Problems with SELinux should be documented in the associated log file, **audit.log**, in the
**/var/log/audit** directory.

First, the audit search (ausearch) command can help filter for specific types of problems.
For example, the following command lists all SELinux events associated with the use of the
sudo command:

::

   ausearch -m avc -c sudo


In contrast, the **sealert -a /var/log/audit/audit.log** command may provide more clarity.


The GUI SELinux Administration Tool
-------------------------------------

If you’ve taken the time to learn SELinux from the command line, this section should be just
a review. For many users, the easiest way to change SELinux settings is with the SELinux
Administration tool, which you can start with the **system-config-selinux** command

::

   yum install policycoreutils-gui
   system-config-selinux

