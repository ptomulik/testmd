scons-tool-cxxtestgen
=====================

.. image:: https://badge.fury.io/py/scons-tool-cxxtestgen.svg
    :target: https://badge.fury.io/py/scons-tool-cxxtestgen
    :alt: PyPi package version

.. image:: https://travis-ci.org/ptomulik/scons-tool-cxxtestgen.svg?branch=master
    :target: https://travis-ci.org/ptomulik/scons-tool-cxxtestgen
    :alt: Travis CI build status

SCons_ tool for cxxtestgen_ command.

Installation
------------

There are few ways to install this tool for your project.

From pypi_
^^^^^^^^^^

This method may be preferable if you build your project under a virtualenv. To
add cxxtestgen tool from pypi_, type (within your wirtualenv):

.. code-block:: shell

      pip install scons-tool-loader scons-tool-cxxtestgen

or, if your project uses pipenv_:

.. code-block:: shell

      pipenv install --dev scons-tool-loader scons-tool-cxxtestgen

Alternatively, you may add this to your ``Pipfile``

.. code-block::

    [dev-packages]
    scons-tool-loader = "*"
    scons-tool-cxxtestgen = "*"


The tool will be installed as a namespaced package ``sconstool.cxxtestgen``
in project's virtual environment. You may further use scons-tool-loader_
to load the tool.

As a git submodule
^^^^^^^^^^^^^^^^^^

#. Create new git repository:

   .. code-block:: shell

      mkdir /tmp/prj && cd /tmp/prj
      touch README.rst
      git init

#. Add the `scons-tool-cxxtestgen`_ as a submodule:

   .. code-block:: shell

      git submodule add git://github.com/ptomulik/scons-tool-cxxtestgen.git site_scons/site_tools/cxxtestgen

Usage example
-------------

#. Create simple test file

   .. code-block:: cpp

      // MyTestSuite1.t.h
      #include <cxxtest/TestSuite.h>
      class MyTestSuite1 : public CxxTest::TestSuite
      {
      public:
        void testAddition(void)
        {
          TS_ASSERT(1 + 1 > 1);
          TS_ASSERT_EQUALS(1 + 1, 2);
        }
      };

#. Create simple SConstruct file

   .. code-block:: python

      # SConstruct
      # TODO: uncomment following lines if the tool is installed via pip/pipenv
      # import sconstool.loader
      # sconstool.loader.extend_toolpath(transparent=True)
      env = Environment(tools = ['cxxtestgen'])
      env.CxxTestGen('MyTestSuite')

#. Try it out:

   .. code-block:: shell

      scons

LICENSE
-------

Copyright (c) 2018 by Pawel Tomulik <ptomulik@meil.pw.edu.pl>

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

.. _scons-tool-cxxtestgen: https://github.com/ptomulik/scons-tool-cxxtestgen
.. _scons-tool-loader: https://github.com/ptomulik/scons-tool-loader
.. _SCons: http://scons.org
.. _pipenv: https://pipenv.readthedocs.io/
.. _pypi: https://pypi.org/
.. _cxxtestgen: http://cxxtest.com/guide.html#cxxtestgen

.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
