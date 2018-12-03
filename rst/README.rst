korowai/docker-sami
===================

Docker container with sami_ documentation generator. The container is designed
to build PHP API documentation for Korowai_ and `Korowai Framework`_ out of the
box. It may be easily adjusted to support other projects.

Features
--------

With this container you can:

- build documentation once and exit,
- build documentation once and then serve it with http server,
- build documentation continuously (rebuilding when sources change),
- build documentation continuously and serve it at the same time.

The default behaviour is to build continuously and serve at the same time.

Quick example
-------------

You may use the container directly on the following directory structure (the
only important is assumption that php source files are found under ``src`` and
we expect the documentation to be generated somewhere under ``docs``, which may
be initially empty).

.. code-block:: console

  user@pc:$ tree .
  .
  |-- docs
  `-- src
      `-- Foo
          `-- Bar.php


Running by hand
^^^^^^^^^^^^^^^

Run it as follows

.. code-block:: console

   user@pc:$ docker run -v "$(pwd):/home/sami/project" -p 8001:8001 --rm korowai-sami


Running with docker-compose_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In the top level directory create ``docker-compose.yml`` containing the
following

.. code-block:: yaml

   version: '3'
   # ....
   services:
      # ...
      sami:
         image: korowai/sami
         ports:
            - "8001:8001"
         volumes:
            - ./:/home/sami/project

Then run

.. code-block:: console

   user@pc:$ docker-compose up sami

Results
^^^^^^^

Whatever method you chose to run the container, you shall see two new
directories

.. code-block:: console

   user@pc:$ ls -d docs/*
   docs/build docs/cache

As long as the container is running, the documentation is available at

-  http://localhost:8001.

Volume mount points exposed
---------------------------

- ``/home/sami/project`` - bind top level directory of your project here.

Working directory
-----------------

- ``/home/sami/project``

Software included
-----------------

- php_
- git_
- sami_


Files inside container
----------------------

In ``/usr/local/bin``
^^^^^^^^^^^^^^^^^^^^^

- scripts which may be used as container's command:

  - ``sami-autobuild`` - builds documentation continuously (watches source directory for changes),
  - ``sami-autoserve``  - builds documentation continuously and runs http server,
  - ``sami-build``  - builds documentation once and exits,
  - ``sami-serve``  - builds source once and starts http server,

- other files

  - ``sami-defaults`` - initializes ``SAMI_xxx`` variables (default values),
  - ``sami-entrypoint`` - provides an entry point for docker.

In ``/home/sami``
^^^^^^^^^^^^^^^^^

- ``sami.conf.php`` - default configuration file for sami.

Configuration variables
-----------------------

The container defines several build arguments which are copied to corresponding
environment variables within the running container. All the arguments/variables
have names starting with ``SAMI_`` prefix. All the script, and the
configuration file ``sami.conf.php`` uses these variables, so the easiest way
to adjust the container to your needs is to rebuild the image with custom
values applied to appropriate ``SAMI_xxx`` arguments.

+--------------------+----------------------------------+---------------------------------------------------------+
|     Variable       |          Default Value           |                   Description                           |
+====================+==================================+=========================================================+
| SAMI_UID           | 1000                             | UID of the user running commands within the container.  |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_GID           | 1000                             | GID of the user running commands within the container.  |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_CONFIG        | /home/sami/sami.conf.php         | Path to the config file for sami.                       |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_PROJECT_TITLE | API Documentation                | Title for the generated documentation.                  |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_SOURCE_DIR    | src                              | Top-level directory with the PHP source files.          |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_BUILD_DIR     | docs/build/html/api              | Where to output the generated documentation.            |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_CACHE_DIR     | docs/cache/html/api              | Where to write cache files.                             |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_SERVER_PORT   | 8001                             | Port numer (within container) for the http server.      |
+--------------------+----------------------------------+---------------------------------------------------------+
| SAMI_SOURCE_REGEX  | \.\(php\|txt\|rst\)$             | Regular expression for the source file names.           |
+--------------------+----------------------------------+---------------------------------------------------------+

How it works
------------

.. _php: https://php.net/
.. _git: https://git-scm.com/
.. _sami: https://github.com/FriendsOfPHP/Sami/
.. _Korowai: https://github.com/korowai/korowai/
.. _Korowai Framework: https://github.com/korowai/framework/
.. _docker-compose: https://docs.docker.com/compose/

.. <!--- vim: set ft=rst ts=2 sw=2 expandtab spell: -->
