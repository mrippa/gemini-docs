Installing EPICS Base on the ADE
================================

EPICS Installation
~~~~~~~~~~~~~~~~~~

Install EPICS R3.15.8 (alternatively, `Install EPICS R3.15.x <Install_EPICS_R3.15.x>`__ or `Install EPICS R7.x <Install_EPICS_R7.x>`__ and ignore the rest of these instructions)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

   mkdir -p /gem_base/epics/R3.15.8
   cd /gem_base/epics/R3.15.8
   git clone https://github.com/gemini-rtsw/epics-base.git
   cd epics-base
   git remote add upstream https://github.com/epics-base/epics-base
   git checkout master
   git fetch upstream
   git checkout GS3.14
   git checkout -b GS3.14-pdev2 GS3.14
   git merge upstream/3.14               <-- This will show an error that we'll fix in the next steps
   git add configure/CONFIG_SITE_ENV
   git commit

make sure base/configure/os/CONFIG_SITE.Common.RTEMS has:

::

   # Where to find RTEMS
   #
   RTEMS_VERSION = 4.10
   RTEMS_BASE = /gem_base/targetOS/RTEMS/rtems-$(RTEMS_VERSION)
   .
   .
   .
   # Select the command-line-input library to use
   #
   #COMMANDLINE_LIBRARY = EPICS
   #COMMANDLINE_LIBRARY = LIBTECLA
   COMMANDLINE_LIBRARY = READLINE

Set the timezone in base/configure/CONFIG_SITE_ENV

::

   EPICS_TIMEZONE=UTC::0:

Set the development snapshot string in
base/configure/CONFIG_BASE_VERSION:

::

   EPICS_DEV_SNAPSHOT=-GS

Now build EPICS

::

   make
   make install

Install Gemini Specific changes to EPICS BASE
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

   chmod +w /gem_base/epics/R3.15.8/base/templates/makeBaseApp/top/configure/*
   export GEM_EPICS_RELEASE=R3.15.8
   cd /gem_base/ade/Gemini-ADE-1.5/makeGemApp
   make

Install TDCT
^^^^^^^^^^^^

| Use the instructions to to install TDCT according to the new scheme
| `New_Scheme_For_TDCT_Installation <New_Scheme_For_TDCT_Installation>`__

Install MSI
^^^^^^^^^^^

::

   cd /gem_base/epics/R3.15.8/extensions/src
   svn co http://sbfsvn02/gemini-sw/gem/vendor/support/msi/1-7 msi
   cd msi
   make

