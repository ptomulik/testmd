scons-tool-gcov
===================

Integrate gcov_ into your SCons_ based project.

Overview
--------

gcov_ is a tool you can use in conjuction with gcc_ (or clang_) to test code
coverage in your programs. It helps discover where your optimization efforts
will best affect your code.

gcov_ uses two files for profiling, see `gcov files`_.  The names of these
files are derived from the original *object* file by substituting the file
suffix with either ``.gcno``, or ``.gcda``. The ``.gcno`` notes file is
generated when the source file is compiled. The ``.gcda`` count data file is
generated when a program containing object files is executed. A separate
``.gcda`` file is created for each object file.

Tasks accomplished by **scons-tool-gcov** are two-fold. First, it helps to
include the above gcov files in project's dependency tree. Builders that depend
on coverage data (for example gcov report builders or test runners) may be
executed in correct order. This also helps to clean-up coverage data when the
project gets cleaned up. The second task is to generate gcov reports by running
gcov program.

Installation
------------

Git-based projects
^^^^^^^^^^^^^^^^^^

#. Create new git repository::

      mkdir /tmp/prj && cd /tmp/prj
      touch README.rst
      git init

#. Add **scons-tool-gcov** as submodule::

      git submodule add git://github.com/ptomulik/scons-tool-gcov.git site_scons/site_tools/gcov

Usage
-----

#. Create some source files, for example ``src/test.hpp``:

   .. code-block:: cpp

      // src/test.hpp
      /**
       * @brief Test class
       */
       class TestClass { };

#. Write ``SConstruct`` file:

   .. code-block:: python

      # SConstruct
      env = Environment(tools = [ 'doxyfile', 'doxygen'])
      SConscript('src/SConscript', exports=['env'], variant_dir='build', duplicate=0)

#. Write ``src/SConscript``:

   .. code-block:: python

      # src/SConscript
      Import(['env'])
      doxyfile = env.Doxyfile( INPUT = '.', RECURSIVE = True)
      env.Doxygen(doxyfile)

#. Try it out::

      scons

Module description
------------------

Construction variables
^^^^^^^^^^^^^^^^^^^^^^

============================== ================================================================================
 Option                         Description
============================== ================================================================================
 GCOV                            Path to gcov_ program.
 GCOVCOM                         Command used to run gcov_ program.
 GCOVCOMSTR                      String to be displayed when running gcov_.
 GCOVFLAGS                       Additional flags to gcov_ program.
 GCOVDEP_DISABLE                 Disable gcov dependency injector.
 GCOVDEP_SOURCE_SUFFIXES         List of source file suffixes for which dependency injector should be enabled.
 GCOVDEP_EXCLUDE                 Files which should be ignored by dependency injector.
 GCOVDEP_EXCLUDE_GCNO            ``*.gcno`` files which should be ignored by dependency injector.
 GCOVDEP_EXCLUDE_GCDA            ``*.gcda`` files which should be ignored by dependency injector.
 GCOVDEP_EXCLUDE_OBJECT          Object files which should not inject their ``*.gcno``'s into dependency tree.
 GCOVDEP_NOCLEAN                 List of gcov files which shouldn't be cleaned up.
 GCOVDEP_OBJECT_BUILDERS         List of object builders which generate ``*.gcno`` files.
 GCOVDEP_RUNNERS                 List of program runners which generate ``*.gcda`` files.
============================== ================================================================================

LICENSE
-------

Copyright (c) 2014 by Pawel Tomulik <ptomulik@meil.pw.edu.pl>

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

.. <!-- Links -->
.. _SCons: http://scons.org
.. _gcov: http://gcc.gnu.org/onlinedocs/gcc/Gcov.html
.. _gcc: http://gcc.gnu.org/
.. _clang: http://clang.llvm.org/
.. _gcov files: http://gcc.gnu.org/onlinedocs/gcc/Gcov-Data-Files.html#Gcov-Data-Files

.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
