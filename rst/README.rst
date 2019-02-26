Veetou package for Oracle DB
````````````````````````````

Compound kinds
^^^^^^^^^^^^^^

+-----------------------+----------------------------------------------------------------+
| Compound kind         | Description                                                    |
+=======================+================================================================+
| Table (regular)       | Regular DB table with data.                                    |
+-----------------------+----------------------------------------------------------------+
| Table (junction)      | Establishes relations between two or more other tables.        |
+-----------------------+----------------------------------------------------------------+
| View                  | Regular view.                                                  |
+-----------------------+----------------------------------------------------------------+
| Type                  | A regular PL/SQL type. May be an object type.                  |
+-----------------------+----------------------------------------------------------------+
| Type (base)           | A base type for a regular object type.                         |
+-----------------------+----------------------------------------------------------------+
| Type (junction)       | An object type used to define a junction table.                |
+-----------------------+----------------------------------------------------------------+
| Type (junction base)  | A base type for junction object.                               |
+-----------------------+----------------------------------------------------------------+
| Type (view)           | An object type used to define a view.                          |
+-----------------------+----------------------------------------------------------------+
| Type (view base)      | A base type for view object.                                   |
+-----------------------+----------------------------------------------------------------+
| Package               | A PL/SQL package with functions, procedures, etc.              |
+-----------------------+----------------------------------------------------------------+

Naming conventions
^^^^^^^^^^^^^^^^^^

Naming conventions by compound kind

+-----------------------+--------+-----------+-------------+-----------------------------+
| Compound kind         | Case   |  Prefix   | Suffix      |         Example             |
+=======================+========+===========+=============+=============================+
| Table (regular)       | lower  | ``v2u_``  |             | ``v2u_classes_map``         |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Table (junction)      | lower  | ``v2u_``  | ``_j``.     | ``v2u_ko_classes_map_j``    |
+-----------------------+--------+-----------+-------------+-----------------------------+
| View                  | lower  | ``v2u_``  | ``_v``      | ``v2u_ko_classes_map_v``    |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Type (regular)        | mixed  | ``V2u_``  | ``_t``.     | ``V2u_Classes_Map_t``       |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Type (regular base)   | mixed  | ``V2u_``  | ``_B_t``.   | ``V2u_Classes_Map_B_t``     |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Type (junction)       | mixed  | ``V2u_``  | ``_J_t``.   | ``V2u_Ko_Classes_Map_J_t``  |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Type (junction base)  | mixed  | ``V2u_``  | ``_I_t``.   | ``V2u_Ko_Classes_Map_I_t``  |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Type (view)           | mixed  | ``V2u_``  | ``_V_t``.   | ``V2u_Ko_Classes_Map_V_t``  |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Type (view base)      | mixed  | ``V2u_``  | ``_U_t``.   | ``V2u_Ko_Classes_Map_U_t``  |
+-----------------------+--------+-----------+-------------+-----------------------------+
| Package               | mixed  | ``V2U_``  |             | ``V2U_Util``                |
+-----------------------+--------+-----------+-------------+-----------------------------+

Although SQL is case-insensitive, we still follow the above case-sensitivity
rules in our SQL scripts. This helps guessing the type of object identified
by a given name.


Veetou DB modules
^^^^^^^^^^^^^^^^^

The database objects of veetou are organized into "modules". Think about a
module as of a set of related types, tables and views.

+------+---------------------+-----------------------------------------------------------+
| ID   | Module name         | Description                                               |
+======+=====================+===========================================================+
| KO   | Karty Osiągnięć     | A set of tables with data obtained from so-called "Karty  |
|      |                     | Osiągnięć" (a kind of mass-report PDF files exported from |
|      |                     | the source system).                                       |
+------+---------------------+-----------------------------------------------------------+
| DZ   | USOS DZ             | A set of USOS destination tables. Most of these tables    |
|      |                     | bear names starting with ``dz_`` prefix.                  |
+------+---------------------+-----------------------------------------------------------+
| UU   | USOS Update         | A set of tables produced by our SQL scripts. These tables |
|      |                     | are designed to resemble DZ tables. UU tables store an    |
|      |                     | initially processed data merged from different sources    |
|      |                     | (KO tables, etc.). The content of UU tables may be used   |
|      |                     | update USOS DZ tables quite easily. The UU tables         |
|      |                     | also provide extra columns for diagnostics (debugging).   |
+------+---------------------+-----------------------------------------------------------+

.. <!--- vim: set spell expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
