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

The default behavior is to build continuously and serve at the same time.

Quick example
-------------

Assume we have the following file hierarchy (the essential here is assumption
that php source files are found under ``src``, also  we expect the
documentation to be written-out somewhere under ``docs``)

.. code-block:: console

  user@pc:$ tree .
  .
  |-- docs
  `-- src
      `-- Foo
          `-- Bar.php


Running with docker_
^^^^^^^^^^^^^^^^^^^^

Run it as follows

.. code-block:: console

   user@pc:$ docker run -v "$(pwd):/home/sami/project" -p 8001:8001 --rm korowai/sami


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

The documentation is written to ``docs/build/html/api``

.. code-block:: console

   user@pc:$ find docs -name 'index.html'
   docs/build/html/api/index.html

As long as the container is running, the documentation is available at

-  http://localhost:8001.


Customizing
-----------

Several parameters can be changed via environment variables, for example

.. code-block:: console

   user@pc:$ docker run -v "$(pwd):/home/sami/project" -p 8001:8001 --rm -e SAMI_BUILD_DIR=/tmp/build korowai/sami

Details
-------

Volume mount points exposed
^^^^^^^^^^^^^^^^^^^^^^^^^^^

- ``/home/sami/project`` - bind top level directory of your project here.

Working directory
^^^^^^^^^^^^^^^^^

- ``/home/sami/project``

Files inside container
^^^^^^^^^^^^^^^^^^^^^^

In ``/usr/local/bin``
"""""""""""""""""""""

- scripts which may be used as container's command:

  - ``sami-autobuild`` - builds documentation continuously (watches source directory for changes),
  - ``sami-autoserve``  - builds documentation continuously and runs http server,
  - ``sami-build``  - builds documentation once and exits,
  - ``sami-serve``  - builds source once and starts http server,

- other files

  - ``sami-defaults`` - initializes ``SAMI_xxx`` variables (default values),
  - ``sami-entrypoint`` - provides an entry point for docker.

In ``/home/sami``
"""""""""""""""""

- ``sami.conf.php`` - default configuration file for sami.

Environment variables
^^^^^^^^^^^^^^^^^^^^^

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

Software included
^^^^^^^^^^^^^^^^^

- php_
- git_
- sami_

.. _php: https://php.net/
.. _git: https://git-scm.com/
.. _sami: https://github.com/FriendsOfPHP/Sami/
.. _Korowai: https://github.com/korowai/korowai/
.. _Korowai Framework: https://github.com/korowai/framework/
.. _docker: https://docker.com/
.. _docker-compose: https://docs.docker.com/compose/

.. <!--- vim: set ft=rst ts=2 sw=2 expandtab spell: -->
