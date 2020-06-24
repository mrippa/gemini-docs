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
Of course, a installation of :code:`git` is needed to migrate to it. This is done easily with

::
  
  sudo yum install -y git
  
svn
^^^
On a typical gemini development machine, :code:`subversion` should already be installed. If not, install it using the equivalent to the command above

::

  sudo yum install -y subversion
  
Migration Procedure
-------------------
In the following, the `gpol` project will be used as example to demonstrate a possible procedure for the repository migration.

Authors File Creation
^^^^^^^^^^^^^^^^^^^^^
First, a authors file needs to be created from the svn commits:

::
  
  svn log -q http://sbfsvn02/gemini-sw/gem/trunk/ioc/gpol | awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > authors.txt
  
This creates a file :code:`authors.txt` in the current directory and should be edited that it contains authors with their email adresses:

::

  jdoe = John Doe <jdoe@example.com>
  jeod = John Eod <jeod@example.com>

Find First Commit
^^^^^^^^^^^^^^^^^
THe next step is to find the revision number in the subversion repository, when the project was created (i.e. the initial commit). Because
initial steps are taken on the :code:`trunk` (and not in ):

::

  svn log --stop-on-copy http://sbfsvn02/gemini-sw/gem/trunk/ioc/gpol
  
The end of the output for this example looks like this:

::
  
  [...]
  ------------------------------------------------------------------------
  r5448 | jdoe | 2020-04-17 23:52:44 +0200 (Fri, 17 Apr 2020) | 1 line
  
  initial commit, not compilable, yet
  ------------------------------------------------------------------------
  r5447 | jeod | 2020-04-17 23:48:17 +0200 (Fri, 17 Apr 2020) | 1 line
  
  gpol/mk: changed contact and set svn:ignore
  ------------------------------------------------------------------------
  r5446 | jdoe | 2020-04-17 23:48:02 +0200 (Fri, 17 Apr 2020) | 1 line
  
  Initial structure of new gpol/mk
  ------------------------------------------------------------------------


So obviously, the revision number wanted in this case is r5446.
