korowai/docker-sami
===================

Docker container with sami_ PHP API doc generator. The container is designed
to build PHP API documentation for Korowai_ and `Korowai Framework`_ out of the
box. It may be easily tweaked to support other projects as well.

With this container you can:

- build documentation once,
- build documentation once and then serve it with http server,
- build documentation continuously (rebuilding when sources change),
- build documentation continuously and serve it in the same time.

Base image
----------

The containers provided by this project are based on ``php:x.y-alpine`` images
(``php:7.1-alpine``, ``php:7.2-alpine``, ...).

Software included
-----------------

- php_
- git_
- sami_


Files inside container
----------------------

Files under ``/usr/local/bin``:

- *sami-autobuild* - builds documentation continuously (watches source directory for changes),
- *sami-autoserve*  - builds documentation continuously and runs http server,
- *sami-build*  - builds documentation once and exits,
- *sami-serve*  - builds source once and starts http server,
- sami-defaults - helper script, applies defaults to ``SAMI_xxx`` environment variables,
- sami-entrypoint - entry point for docker,

The files in *italics* are scripts that may be used as container's command. The
default command run by container is *sami-autoserve*.

Files under ``/home/sami``

- ``sami.conf.php``

Configuration variables
-----------------------

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

.. <!--- vim: set ft=rst ts=2 sw=2 expandtab spell: -->
