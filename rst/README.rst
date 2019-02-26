Veetou package for Oracle DB
````````````````````````````

Naming conventions
^^^^^^^^^^^^^^^^^^

All veetou-related DB symbols related start with ``v2u_`` prefix. Althrough the
PL/SQL is case-insensitive, we still stick to coding conventions summarized in
the following table

+-----------------+----------------------------------------------------------+------------------------------+
| Compound kind   |                 Naming Convention                        |         Example              |
+=================+==========================================================+==============================+
| Table (regular) | Case: lower, prefix: ``v2u_``, suffix: none.             | ``v2u_classes_map``          |
+-----------------+----------------------------------------------------------+------------------------------+
| Table (junction)| Case: lower, prefix: ``v2u_``, suffix: ``_j``.           | ``v2u_ko_classes_map_j``     |
+-----------------+----------------------------------------------------------+------------------------------+
| View            | Case: lower, prefix: ``v2u_``, suffix: ``_v``            | ``v2u_subject_semesters_v``  |
+-----------------+----------------------------------------------------------+------------------------------+
| Type (regular)  | Case: capitalized, prefix: ``V2u_``, suffix: ``_t``.     | ``V2u_Classes_Map_t``        |
+-----------------+----------------------------------------------------------+------------------------------+
| Type (base)     | Case: capitalized, prefix: ``V2u_``, suffix: ``_B_t``.   | ``V2u_Classes_Map_B_t``      |
+-----------------+----------------------------------------------------------+------------------------------+
| Type (junction) | Case: capitalized, prefix: ``V2u_``, suffix: ``_J_t``.   | ``V2u_Ko_Classes_Map_J_t``   |
+-----------------+----------------------------------------------------------+------------------------------+
| Type (junction  | Case: capitalized, prefix: ``V2u_``, suffix: ``_J_t``.   | ``V2u_Ko_Classes_Map_J_t``   |
| base)           |                                                          |                              |
+-----------------+----------------------------------------------------------+------------------------------+
| Package         | Case: capitalized, prefix: ``V2U_``, suffix: none.       | ``V2U_Util``                 |
+-----------------+----------------------------------------------------------+------------------------------+
