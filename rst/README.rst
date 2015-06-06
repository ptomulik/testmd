yaul-packaging
==============

This projects helps with packaging and release process of the yaul_ library.

Branches
--------

The repository contains several separate branches for different purposes

+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| ``debian-debian/<release>``       | the Debian packaging for a <release> (e.g., sid or experimental)                                          |
+=================================+===========================================================================================================+
| ``debian-upstream/<release>``   | the upstream sources for a release matching one of the above                                              |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| ``debian-security/<release>``   | security updates for a certain release                                                                    |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| ``debian-backports/<release>``  | backports to a certain release                                                                            |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| ``debian-dfsg/<release>``           | the dfsg clean upstream sources in case the cleanup is done via a Git merge from upstream to this branch. |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+


Tasks
-----

create soruce tarball
`````````````````````

- Create tarball out of upstream's ``yaul-0.1.0`` tag

.. code:: shell

    ./scripts/create-tarball yaul-0.1.0

- Create source tarball from most recent upstream commit

.. code:: shell

    ./scripts/create-tarball master

The script requires an access to temporary directory (e.g., ``/tmp``).


Start packaging for a new debian release
````````````````````````````````````````

- Prepare a source tarball

.. code:: shell

    ./scripts/create-tarball yaul-0.1.0
    mv ../yaul-0.1.0.tar.gz ../yaul_0.1.0.orig.tar.gz

- create branch for upstream sources

.. code::

    git checkout --orphan debian-upstream/sid

- create branch for debian packaging, for example

.. code::

    git checkout -b debian-debian/sid

- initialize ``debian/`` directory

.. code:: shell

    dh_make -m -e ptomulik@meil.pw.edu.pl -p yaul_0.1.0


Build package
`````````````

.. code::

    git checkout debian-debian/sid
    gbp buildpackage

New release
```````````


.. _yaul: https://github.com/ptomulik/yaul
