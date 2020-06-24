Migrating from SVN to git
======================================
Possibilities
-------------
git-svn
^^^^^^^
:code:`git-svn` is part of :code:`git` and well `documented <https://git-scm.com/docs/git-svn>`_ within that versioning control system. 
svn2git
^^^^^^^
:code:`svn2git` is much more comfortable to use, but takes a directory structure as given, which does not exist for 
ADE-developed software modules at Gemini. Hence, this tool cannot be used for this pupose.

Software Requirements
---------------------
Operating System
^^^^^^^^^^^^^^^^
The following procedure assumes CentOS7 as operating system for the migration steps. Principally, things should be working 
with other linux distributions or even operating systems as well, but to prevent against svn or git version conflicts the 
steps are taken under the mentioned one.
git
^^^
Of course, a installation of git is needed to migrate to it. This is done easily with

::
  
  sudo yum install -y git
  
svn
^^^
On a typical gemini development machine, subversion should already be installed. If not, install it using the equivalent to the command above

::

  sudo yum install -y subversion
  
Migration Procedure
-------------------
