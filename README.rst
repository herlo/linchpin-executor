Linchpin Executor Container
===========================

This document covers what is in this repository for building the linchpin-executor container.
The linchpin-executor is used by the `contra-hdsl library <https://github.com/openshift/contra-hdsl>`_ to setup the `contra-env-sample-project <https://github.com/CentOS-PaaS-SIG/contra-env-sample-project>`_ as a tool for provisioning cloud-based resources. It uses `linchpin <https://github.com/CentOS-PaaS-SIG/linchpin>`_ to provision these resources within the container.

Building the linchpin-executor image
------------------------------------

A Makefile, located at config/Dockerfiles/linchpin-executor/Makefile, is provided to build and test the linchpin-executor image. The test containers are removed upon successful testing, leaving the linchpin-executor image in the local registry.

There are two targets in the Makefile, `buildcontainer` and `testcontainer`. The `buildcontainer` target does exactly that, builds the container using `buildah`. The `testcontainer` target will build a container, then test it to make sure it is valid. This will be useful when running the Jenkinsfile (also included in this repository).

Running the LinchPin Container image
------------------------------------

Buildah can run the container.*

.. code-block:: bash

    $ sudo buildah from contrainfra/linchpin-executor
    linchpin-executor-working-container
    $ sudo buildah run linchpin-working-container -v /path/to/workspace:/workdir -- linchpin -w /workdir up
    .. snip ..

.. note:: `Buildah <https://github.com/containers/buildah>`_ is a daemonless equivalent to Docker, but currently must run as root.

Continuous Delivery
-------------------

The goal of this tooling is to create and update the `linchpin-executor` image, to be distributed via the `docker hub <https://hub.docker.com/r/herlo/linchpin-executor/>`_. This is done by using a Jenkins multibranch pipeline, which can be found in the root of this repository. The Jenkinsfile, uses code from the `contra-lib library <https://github.com/openshift/contra-lib>`_ to ensure the latest `linchpin-executor` container is built, tested, and uploaed to the appropriate location when changes are made.
