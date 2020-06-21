Using Docker/Podman for ADE
===========================

Introduction
------------
Citing `wikipedia <https://en.wikipedia.org/wiki/Docker_%28software%29>`_, "docker (as well as podman) is a set of platform as a service (PaaS) products that uses OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels. All containers are run by a single operating system kernel and therefore use fewer resources than virtual machines."

This lets docker and similar approaches like podman be a good solution for various tasks in the new ADE generation, presumably.

Setup
-----
Installation
^^^^^^^^^^^^
For exact installation instructions refer to descriptions of the host operating system. One of the advantages of the usage of docker is namely the fact, that docker containers work abstracted from the host OS. Note in this context that podman is syntax-compatible to docker, so if prodman is (or will be) preferred, substitute :code:`docker` with :code:`podman` in the following. I made the experience, though, that - at least under Ubuntu - podman needs quite long (several dozens of seconds) to start up, while docker starts execution almost immediately. Further investigation is needed here, because conceptwise, podman seems to be much safer and convincing, as no root level service is used to manage image execution and associated actions.

Definition
^^^^^^^^^^
A `Dockerfile <https://docs.docker.com/engine/reference/builder/>`_ is used to automatically create a docker image from a ASCII text file. The following one for initial tests was used to create an image (:code:`centos7` with tag :code:`ADE`) based on the current ADE - which is copied over to the image as a tarball at creation time with all necessary tools and EPICS modules readily installed - and a centos7 base image distributed via `dockerhub <https://hub.docker.com/_/centos>`_:

::

  FROM centos:7
  
  # add local ADE installation to image. 
  # TODO: install following 'official' process
  COPY ./gem_sw /gem_sw
  COPY ./ade.sh /etc/profile.d/
  RUN groupadd -g600 gemdev
  RUN groupadd -g980 gembuild
  RUN groupadd -g993 local-admins
  RUN groupadd -g994 local-users
  RUN groupadd -g1502 ssh-users
  RUN groupadd -g1506 detlab
  RUN groupadd -g3624 software
  RUN groupadd -g2000 gemini
  RUN useradd -u600 -g600 gemdev
  
  
  RUN yum -y update; yum clean all
  
  RUN yum -y install gcc gcc-c++ glibc-devel make git cmake3 java-1.8.0-openjdk-headless readline-devel; yum clean all
  
  RUN yum -y install iproute net-tools; yum clean all
  
  CMD ["/bin/bash"]

Building
^^^^^^^^
A docker image can be `built <https://docs.docker.com/engine/reference/commandline/build/>`_ in the directory where the Dockerfile resides, for example by executing the command:
::

  docker build -t centos7:ADE .
  
Hosting
^^^^^^^
Apart from `dockerhub <https://hub.docker.com/_/centos>`_, the ready built images can be hosted on an own socalled `registry <https://docs.docker.com/registry/>`_. In Gemini's context, this could ensure a well-defined build environment which could be used locally to develop with ADE and to deploy RPMs, for example, after building a local image derived from the base image provided via that registry. Like :code:`centos:7` in the given example, a self-created image could be used as base for various images with specific tasks in Gemini's development environment. Those images, of course, could also be hosted in that registry.

Usage
=====
The docker image is now known by your docker platform and can be used. One example is of course, to use it to build a IOC module with the ADE installed in this docker image, with the sources not residing in the docker image, but on your host file system. With the following command, the gcal ioc modules was built with its code at :code:`$PWD/gcal` mounted into the docker image at startup time and hence shared between host and guest OS:
::
  
  docker run --rm -v $PWD/gcal:/gem_sw/work/R3.14.12.8/ioc/gcal -w /gem_sw/work/R3.14.12.8/ioc/gcal/mk -e EPICS=/gem_sw/epics/R3.14.12.8/ -i -t centos7:ADE /bin/bash -c ". /etc/profile && make distclean uninstall all"
  
To simply get a :code:`bash` to do things more interactively on this project you might just execute
::

  docker run --rm -v $PWD/gcal:/gem_sw/work/R3.14.12.8/ioc/gcal -w /gem_sw/work/R3.14.12.8/ioc/gcal/mk -e EPICS=/gem_sw/epics/R3.14.12.8/ -i -t centos7:ADE /bin/bash

Please refer to the respective `documentation <https://docs.docker.com/engine/reference/run/>`_ for a reference of the various parameters used with these commands. 
