yaul-packaging
==============

This projects helps with packaging and release process of the yaul_ library.

scripts
-------

scripts/create-tarball
^^^^^^^^^^^^^^^^^^^^^^

This script creates source tarball out of the remote yaul_ repository. The tarball is created in outer directory relative to the ``yaul-packaging`` working dir. Here are few examples:

1. Create source tarball from ``yaul-0.1.0`` tag.

.. code:: shell

    ./scripts/create-tarball yaul-0.1.0

2. Create source tarball from HEAD

.. code:: shell

    ./scripts/create-tarball yaul-0.1.0

branches
=========

The repository contains several separate, and completely different branches for different package formats.

- ``debian-debian/<release>``	- the Debian packaging for a <release> (e.g., sid or experimental),
- ``debian-upstream/<release>`` - the upstream sources for a release matching one of the above,
- ``debian-security/<release>`` - security updates for a certain release,
- ``debian-backports/<release>`` - backports to a certain release,
- ``debian-dfsg/<release>``	- the dfsg clean upstream sources in case the cleanup is done via a Git merge from upstream to this branch. 

.. _yaul: https://github.com/ptomulik/yaul
