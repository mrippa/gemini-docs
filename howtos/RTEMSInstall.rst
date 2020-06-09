Installing RTEMS 
============================================

Introduction
------------

These are the steps taken to set up a parallel Application Development
Environment that is completely separate from our production ADE.

Procedure
---------

- ADE machine is sbfade or hbfade
- Confirm /gem_base directory 

Prerequisite ADE base directories
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

   sudo mkdir -p /gem_base/targetOS/RTEMS/source
   sudo groupadd gembuild
   sudo chgrp -R gembuild /gem_base
   sudo chmod -R g+s /gem_base
   sudo chmod -R 775 /gem_base
   sudo usermod -a -G gembuild <username>

Log out, then log back in to pick up the new group.

RTEMS installation
~~~~~~~~~~~~~~~~~~

Make sure the following system packages are installed: 


::

   sudo yum install make automake autoconf python3 python3-devel bison flex gcc gcc-c++ texinfo patch git readline-devel re2c java 
   sudo alternatives --set python /usr/bin/python3

Install cross-compiler tool chain:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

   cd /gem_base/targetOS/RTEMS/source
   git clone -b 4.10 git://git.rtems.org/rtems-source-builder.git
   cd rtems-source-builder/rtems
   ../source-builder/sb-set-builder --log=l-powerpc.txt --without-rtems --prefix=/gem_base/targetOS/RTEMS/rtems-4.10 4.10/rtems-powerpc

Install RTEMS 4.10.2
^^^^^^^^^^^^^^^^^^^^

::


   export PATH=/gem_base/targetOS/RTEMS/rtems-4.10/bin:$PATH

   cd /gem_base/targetOS/RTEMS/source
   mkdir rtems
   cd rtems

   wget --passive-ftp --no-directories --retr-symlinks https://git.rtems.org/rtems/snapshot/rtems-4.10.2.tar.bz2
   tar xjvf rtems-4.10.2.tar.bz2

   cd /gem_base/targetOS/RTEMS/source/rtems/rtems-4.10.2
   ./bootstrap
   mkdir build
   cd build
   ../configure --target=powerpc-rtems4.10 --prefix=/gem_base/targetOS/RTEMS/rtems-4.10 --enable-cxx --enable-rdbg --disable-tests --enable-networking --enable-posix --enable-rtemsbsp="beatnik mvme2307 mvme3100 qemuppc"

   make
   make install

Install RTEMS add-ons
^^^^^^^^^^^^^^^^^^^^^

::

   cd /gem_base/targetOS/RTEMS/source/rtems

   wget https://ftp.rtems.org/pub/rtems/releases/4.10/4.10.2/rtems-addon-packages-4.10.2.tar.bz2

   tar xjvf rtems-addon-packages-4.10.2.tar.bz2
   cd rtems-addon-packages-4.10.2

   export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/beatnik
   ./bit

   export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/mvme2307
   ./bit

   export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/mvme3100
   ./bit

   export RTEMS_MAKEFILE_PATH=/gem_base/targetOS/RTEMS/rtems-4.10/powerpc-rtems4.10/qemuppc
   ./bit



`Install BSP Extensions 1.5 -- (Deprecated -- install BSP Extensions 1.6
instead) <Install_BSP_Extensions_1.5_--_(Deprecated_--_install_BSP_Extensions_1.6_instead)>`__

Install BSP Extensions 1.6
^^^^^^^^^^^^^^^^^^^^^^^^^^

::

   export PATH=/gem_base/targetOS/RTEMS/rtems-4.10/bin:$PATH
   cd /gem_base/targetOS/RTEMS/source/rtems
   wget --passive-ftp --no-directories --retr-symlinks http://www.slac.stanford.edu/~strauman/rtems/ssrlapps-R_libbspExt_1_6.tgz
   tar xzvf ssrlapps-R_libbspExt_1_6.tgz
   cd /gem_base/targetOS/RTEMS/source/rtems/ssrlapps-R_libbspExt_1_6
   mkdir build
   cd build
   ../configure --with-rtems-top=/gem_base/targetOS/RTEMS/rtems-4.10 --prefix=/gem_base/targetOS/RTEMS/rtems-4.10 --with-package-subdir=. --enable-std-rtems-installdirs
   make
   make install


