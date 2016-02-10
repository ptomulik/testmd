clxx - c++11 wrappers for OpenCL
================================

.. image:: https://travis-ci.org/ptomulik/clxx.png?branch=master
    :target: https://travis-ci.org/ptomulik/clxx

.. image:: https://coveralls.io/repos/ptomulik/clxx/badge.png?branch=master
   :target: https://coveralls.io/r/ptomulik/clxx?branch=master



Welcome to the clxx development tree.

START
-----

Start with cloning this repository (note: we use git submodules so we need
``--recursive`` flag):

.. code:: bash

    git clone --recursive git://github.com/ptomulik/clxx.git

REQUIREMENTS
------------

To build the project
`````````````````````

X. Boost libraries:

- `Boost.Algorithm`_
- `Boost.Serialization`_
- `Boost.Bimap`_
- `Boost.Core`_
- `Boost.Config`_
- `Boost.Smart Ptr`_
- `Boost.Program Options`_
- `Boost.Unordered`_


X. X11 client-side library (development):

- `libx11-dev`_

To download some prerequisites
``````````````````````````````

First of all, you need python to run ``bin/download-deps.py`` script. Then, you
need following software for the packages being downloaded (and possibly
compiled):

=================== ==========================================================
      Package                           Requirements
=================== ==========================================================
  cxxtest
------------------- ----------------------------------------------------------
  opencl-icd-ldr      c/c++ toolchain, make, cmake, autotools, libpcre, OpenGL
                      headers (on Linux),
------------------- ----------------------------------------------------------
  opencl-hdr
------------------- ----------------------------------------------------------
  swig                c/c++ toolchain, make, autotools, bison, yodl
------------------- ----------------------------------------------------------
  scons
=================== ==========================================================

Installing dependencies on Debian
`````````````````````````````````

Boost libraries::

    sudo apt-get install libboost-dev libboost-program-options-dev

or just::

    sudo apt-get install libboost-all-dev

X11 libraries::

    sudo apt-get install libx11-dev

PCRE library::

    sudo apt-get install libpcre3-dev

OpenGL headers::

    sudo apt-get install mesa-common-dev

Bison::

    sudo apt-get install bison

Yodl::

    sudo apt-get install yodl

HOWTO
-----

See files under ``HOWTO/`` directory to read about most common routines. There
are several documents.

For everyone:

==================================== ===========================================
           File                              Description
==================================== ===========================================
 `HOWTO/compile.rst`_                 compiling the sources
==================================== ===========================================

For clxx developers:

==================================== ===========================================
            File                              Description
==================================== ===========================================
 `HOWTO/create-source.rst`_           creating new source file
------------------------------------ -------------------------------------------
 `HOWTO/test.rst`_                    running tests
==================================== ===========================================


DIRECTORY STRUCTURE
-------------------

Top level source directory contains following subdirs:

================= ==============================================================
    Directory      Description
================= ==============================================================
 ``bin/``          contains mainainer scripts and additional utilities,
----------------- --------------------------------------------------------------
 ``build/``        this is main (default) variant directory, all the results of
                   compilation go there; the directory is created by scons,
----------------- --------------------------------------------------------------
 ``HOWTO/``        several HOWTO documents are placed here,
----------------- --------------------------------------------------------------
 ``debian/``       debian packaging files (currently empty),
----------------- --------------------------------------------------------------
 ``rpm/``          rpm packaging files (currently empty)
----------------- --------------------------------------------------------------
 ``site_scons/``   extensions used by scons,
----------------- --------------------------------------------------------------
 ``src/``          main source tree with source files to be compiled,
----------------- --------------------------------------------------------------
 ``template/``     templates for source files,
----------------- --------------------------------------------------------------
 ``valgrind/``     configuration files for valgrind
================= ==============================================================

.. _HOWTO/compile.rst: HOWTO/compile.rst
.. _HOWTO/create-source.rst: HOWTO/create-source.rst
.. _HOWTO/test.rst: HOWTO/test.rst
.. _libboost-dev: https://packages.debian.org/libboost-dev
.. _libx11-dev: https://packages.debian.org/libx11-dev
.. _Boost.Algorithm: http://www.boost.org/doc/libs/release/libs/algorithm/
.. _Boost.Serialization: http://www.boost.org/doc/libs/release/libs/serialization/
.. _Boost.Bimap: http://www.boost.org/doc/libs/release/libs/bimap/
.. _Boost.Core: http://www.boost.org/doc/libs/release/libs/core/
.. _Boost.Config: http://www.boost.org/doc/libs/release/libs/config/config.htm
.. _Boost.Smart Ptr: http://www.boost.org/doc/libs/release/libs/smart_ptr/smart_ptr.htm
.. _Boost.Program Options: http://www.boost.org/doc/libs/release/libs/program_options/
.. _Boost.Unordered: http://www.boost.org/doc/libs/release/libs/unordered/
.. _bison: https://www.gnu.org/software/bison/

LICENSE
-------

@COPYRIGHT@

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE

.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
