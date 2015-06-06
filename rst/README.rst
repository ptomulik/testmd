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

Creating soruce tarball
```````````````````````

- Create tarball out of upstream's ``yaul-0.1.1`` tag::

    git checkout default
    ./scripts/create-tarball yaul-0.1.1

- Create source tarball from most recent upstream commit::

    git checkout default
    ./scripts/create-tarball master

The script requires an access to temporary directory (usually ``/tmp``, see
``mktemp(1)``) where it clones the upstream repository and manipulates files.


Creating soruce tarball for Debian packaging tools
``````````````````````````````````````````````````

- Create normal source tarball and rename it appropriatelly::

    git checkout default
    ./scripts/create-tarball yaul-0.1.1
    mv ../yaul-0.1.1.tar.gz ../yaul0.1_0.1.1.orig.tar.gz

Preparing support for new Debian release
````````````````````````````````````````

- Prepare initial ``debian`` directory::

    git checkout default
    ./scripts/create-debian-release stretch 0.1.1

  this shall create ``debian.stretch`` with the initial contents of ``debian``
  directory for stretch release.

- create branch for upstream sources::

    git checkout --orphan debian-upstream/stretch
    git rm -rf --cached .
    mv gitignore.debian .gitignore
    git commit -m 'initial commit for debian/stretch packaging'

- create branch for debian packaging::

    git checkout -b debian-debian/stretch

- switch to the packaging branch and initialize ``debian/`` directory::

    git checkout -b debian-dfsg/stretch
    mv debian.stretch debian

- prepare a source tarball::

    ./scripts/create-tarball yaul-0.1.1
    mv ../yaul-0.1.1.tar.gz ../yaul0.1_0.1.1.orig.tar.gz

- import the source tarball::

    gbp import-orig ../yaul0.1_0.1.1.orig.tar.gz

- build the package::

    gbp buildpackage


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
