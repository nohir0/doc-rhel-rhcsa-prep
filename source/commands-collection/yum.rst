====
yum
====


Which packages are installed within the **base** package group?
-------------------------------------------------------------------

Use yum group info::

   # yum group info base

**Hint:** On the RHEL installation media there is a XML file called **/repodata/*-comps-Server-x86_64.xml** where <grouplist> and <optionlist> shows the belonging package information