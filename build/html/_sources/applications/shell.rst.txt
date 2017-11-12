##############
shell
##############


Text Streams and redirection
------------------------------

::

   # program 2> error-to-file
   # program 2> /dev/null
   # progeam &> output-and-error



ln - Hardlink vs. Softlink
----------------------------

Hard links are directory entries that point to the same inode. They must be created
within the same filesystem. You could delete a hard-linked file in one directory, and it would
still exist in the other directory (files are only deleted when the number of dentry records
pointing to them hit 0, which is tracked via a counter per file)

On the other hand, a soft link serves as a redirect; when you open a file created with a
soft link, the link redirects you to the original file. If you delete the original file, the file is
lost. Although the soft link is still there, it has nowhere to go



sed - Stream Editor
---------------------
Replace all occurences (g - global) of the word Windows with Linux

::

   # sed 's/Windows/Linux/g' opsys > newopsys

Another example
::

   # sed 's/writable = yes/writable = no/g' /etc/samba/smb.conf > ~/smb.conf



awk - Aho, Weinberger, and Kernighan
-------------------------------------
Following command will read out the fourth field in /etc/passwd (the group ID) of every user with a listing of “mike”

::

   # awk -F : '/mike/ {print $4}' /etc/passwd



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
