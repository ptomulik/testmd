pkg-yaul
========

This projects maintains the packaging and releasing process of yaul_ library.

- for debian packages we use git-buildpackage_,

Branches
--------

The repository contains several separate branches for different purposes

+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| Branch                          | Purpose                                                                                                   |
+=================================+===========================================================================================================+
| *debian-debian/<release>*       | the Debian packaging for a <release> (e.g., jessie, wheezy, sid or experimental)                          |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| *debian-upstream/<release>*     | the upstream sources for a release matching one of the above                                              |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| *debian-security/<release>*     | security updates for a certain release                                                                    |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| *debian-backports/<release>*    | backports to a certain release                                                                            |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+
| *debian-dfsg/<release>*         | the dfsg clean upstream sources in case the cleanup is done via a Git merge from upstream to this branch. |
+---------------------------------+-----------------------------------------------------------------------------------------------------------+


Tasks
-----

create soruce tarball
`````````````````````

- Create tarball out of upstream's ``yaul-0.1.0`` tag

.. code:: shell

    git checkout default
    ./scripts/create-tarball yaul-0.1.0

- Create source tarball from most recent upstream commit

.. code:: shell

    git checkout default
    ./scripts/create-tarball master

The script requires an access to temporary directory (usually ``/tmp``, see
``mktemp(1)``) where it clones the upstream repository and manipulates files.


Start packaging for a new debian release
````````````````````````````````````````

- Prepare a source tarball

.. code:: shell

    ./scripts/create-tarball yaul-0.1.0
    mv ../yaul-0.1.0.tar.gz ../yaul_0.1.0.orig.tar.gz

- create branch for upstream sources

.. code::

    git checkout --orphan debian-upstream/stretch

- create branches for debian packaging, and switch to ``debian-debian`` branch

.. code::

    git checkout -b debian-debian/stretch
    git checkout -b debian-security/stretch
    git checkout -b debian-backports/stretch
    git checkout -b debian-dfsg/stretch


- initialize ``debian/`` directory

.. code:: shell

    git checkout debian-debian/stretch
    mkdir debian/
    git show default:debian.template/gbp.conf | sed -e 's/@DEBIAN_RELEASE@/stretch/g' > debian/gbp.conf

.. <!--- dh_make -m -e ptomulik@meil.pw.edu.pl -p yaul_0.1.0 -->

- put the following contents to debian/gbp.conf

.. 


Build package
`````````````

.. code::

    git checkout debian-debian/stretch
    gbp buildpackage

New release
```````````


.. _yaul: https://github.com/ptomulik/yaul
.. _git-buildpackage: https://honk.sigxcpu.org/piki/projects/git-buildpackage/
.. _gbp-manual: http://honk.sigxcpu.org/projects/git-buildpackage/manual-html/gbp.html

.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
