###########
encryption
###########

.. _ssh-encryption:

Basic Encrypted Communication
-----------------------------

The **ssh-keygen** command is used to set up a public/private key pair. Although it creates an
RSA key by default, it also can be used to create a DSA or ECDSA key.

::

   ssh-keygen
   Generating public/private rsa key pair.
   Enter file in which to save the key (/root/.ssh/id_rsa): 
   Enter passphrase (empty for no passphrase): 
   Enter same passphrase again: 
   Your identification has been saved in /root/.ssh/id_rsa.
   Your public key has been saved in /root/.ssh/id_rsa.pub.
   The key fingerprint is:
   SHA256:498z+yooD85c9e9gotxKe8/CylZK7A2IxJVxd6P7drs root@kvm-server01.example.com
   The key's randomart image is:
   +---[RSA 2048]----+
   |      .o. . o    |
   |      o. . o .   |
   |   . .    .      |
   |    o      .     |
   |   . . oS o      |
   |    . ..+o.o     |
   |      .o+B. * .  |
   |     +.*==**o+ . |
   |      ++O+o+*BE. |
   +----[SHA256]-----+
   [root@kvm-server01 gans]# 

If desired, you can set up RSA keys with a larger number of bits. In our testing, we were
able to set up key pairs with up to 8192 bits fairly quickly, even on a virtual machine system
with just one virtual CPU.

The command that starts the process is

::

   ssh-keygen -b 8192

Alternatively, if a DSA key is needed, the following command can help. Only 1024-bit
DSA keys are allowed. The process after this command is the same as shown in Figure 4-8.

::

   ssh-keygen -t dsa

The next step is to transmit the public key to a remote system. It might be one of the
servers you administer. If you’re willing to transmit that public key over the network
(once per connection), the following command can work:

::

   ssh-copy-id -i .ssh/id_rsa.pub michael@tester1.example.com