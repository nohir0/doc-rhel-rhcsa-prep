########
apache
########

Installation, Start and Autostart::

   # yum -y install httpd
   # systemctl start httpd
   # systemctl enable httpd
   # firewall-cmd --permanent --zone=public --add-service http
   success
   # firewall-cmd --reload
   success


Copy RHEL CDROM content to webserver directory
-------------------------------------------------

Documented under commands-collection yum: :ref:`yum-copy-cdrom`.



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
