TML
===

.. image:: https://travis-ci.org/ptomulik/tml.svg?branch=master
    :target: https://travis-ci.org/ptomulik/tml

Template Metaprogramming Library

Overview
--------

The TML library is a general-purpose C++ template metaprogramming library with
compile-time algorithms, sequences and metafunctions. It's inspired by
`Boost.MPL`_ and `tinympl`_. It tries to preserve the `Boost.MPL`_'s concepts
and interfaces while adding few new concepts and features. The implementation
of TML makes use of the C++11 features, resulting in smaller code base.

Status
------

It's just the beginning. Many things are missing or incomplete.

Features
--------


Sequences
^^^^^^^^^

The TML introduces new sequence classes called ``sequence`` and
``lsequence_c``. The ``sequence`` class models what I call "Variadic Template 
Sequence". The concept of "Template Sequence" makes an explicit assumption,
that the sequence has form of instantiation ``X<a0,...a{n-1}>`` of some
template ``X`` and ``a0,...,a{n-1}`` are then the elements of the sequence.
A "Variadic Template Sequence" is a Template Sequence with ``X`` being a
variadic template.

Classes
```````

  [ ] vector
  [ ] list
  [ ] deque
  [ ] set
  [ ] map
  [ ] range_c
  [ ] vector_c
  [ ] list_c
  [ ] set_c
  [ ] string
  [x] sequence (new)

Views
`````

  [ ] empty_sequence
  [ ] filter_view
  [ ] iterator_range
  [ ] joint_view
  [ ] single_view
  [ ] transform_view
  [ ] zip_view

Intrinsic Metafunctions
```````````````````````

  +====================+========+======+=======+=====+=====+========+==========+
  |                    | vector | list | deque | set | map | string | sequence |
  +====================+========+======+=======+=====+=====+========+==========+
  | apply_sequence     |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | at                 |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | at_c               |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | back               |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | begin              |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | clear              |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | empty              |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | end                |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | erase              |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | erase_key          |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | front              |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | has_key            |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | insert             |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | insert_range       |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | is_sequence        |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | key_type           |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | order              |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | pop_back           |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | pop_front          |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | push_back          |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | push_front         |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | sequence_generator |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | sequence_tag       |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | size               |        |      |       |     |     |        |     +    |
  +--------------------+--------+------+-------+-----+-----+--------+----------+
  | value_type         |        |      |       |     |     |        |          |
  +--------------------+--------+------+-------+-----+-----+--------+----------+



Iterators
^^^^^^^^^

Algorithms
^^^^^^^^^^

Metafunctions
^^^^^^^^^^^^^

Data Types
^^^^^^^^^^


Supported compilers
-------------------

Currently supported compilers:

- gcc >= 4.7
- clang >= 3.4

I haven't tested with other compilers. Feedback is welcome.


Generating reference manual
---------------------------

.. code-block:: shell

    (cd doc/ && doxygen)

The generated documentation is written to ``doc/refman/html``.

Running tests
-------------

.. code-block:: shell

    bjam -a test example

Using specific compiler (e.g. clang):

.. code-block:: shell

    bjam toolset=clang -a test example

License
-------

Copyright (C) 2014, Pawel Tomulik <ptomulik@meil.pw.edu.pl>

Distributed under the Boost Software License, Version 1.0.
(See accompanying file LICENSE_1_0.txt or copy at
`http://www.boost.org/LICENSE_1_0.txt <http://www.boost.org/LICENSE_1_0.txt>`_)

.. _Boost.MPL: http://www.boost.org/libs/mpl/doc/
.. _tinympl: https://github.com/sbabbi/tinympl
