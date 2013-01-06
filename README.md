<!--- 
@COPYRIGHT@

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
..
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE
-->

DIMBO - a DIstributed platform for the simulation of MultiBody systems
######################################################################

Welcome to the DIMBO development tree.

HOWTO
=====

See files under `HOWTO/` directory to read about most common routines. There
are several documents.

For all:

  - compiling: `HOWTO/compile.md`

For developers:

  - creating new source file: `HOWTO/create-source.md`
  - adding new class module: `HOWTO/addclass.md`


DIRECTORY STRUCTURE
===================

Top level source directory contains following subdirs:

  - `bin/` - contains mainainer scripts and additional utilities,
  - `build/`  - this is main (default) variant directory, all the results of
     compilation go here.
  - `HOWTO/` - several HOWTO documents are placed here,
  - `debian/` - debian packaging files (currently empty),
  - `rpm/` - rpm packaging files (currently empty)
  - `site_scons/` - extensions used by scons,
  - `src/`  - main source tree with source files to be compiled,
  - `template/` - templates for source files,
  - `valgrind/` - configuration files for valgrind

LICENSE
=======

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

<!---
vim: set expandtab tabstop=2 shiftwidth=2 syntax=markdown: 
vim: set foldmethod=marker foldcolumn=4:
-->
