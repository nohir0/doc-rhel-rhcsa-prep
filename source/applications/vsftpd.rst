########
vsftpd
########

Installation, Start and Autostart::

   # yum -y install vsftpd
   # systemctl start vsftpd
   # systemctl enable vsftpd
   # firewall-cmd --permanent --zone=public --add-service ftp
   success
   # firewall-cmd --reload
   success




Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
