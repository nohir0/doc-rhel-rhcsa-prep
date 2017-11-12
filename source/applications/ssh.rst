=====
ssh
=====


.. _ssh-tunnel:

ssh tunnel
-----------

You can create a ssh tunnel and then connect to the defined port via localhost::

   ssh -v -C -L 5901:localhost:5901 192.168.100.100

This example establishes a ssh tunnel to 192.168.100.100 for port 5901.
After session establishment you can vnc to localhost:5901 and it uses the port forwarinding through the tunnel.
The option **-L** is the option that makes port forwarding possible



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
