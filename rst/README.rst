INVENTORY
=========

Simple stackholding utility.

Overview
--------

The purpose of *INVENTORY* is to keep track of assets (tangible assets,
materials, etc.), owners, purchases, related documents, formal procedures
and other aspects of stocktaking. The whole project consists of three
components

- `MySQL`_ database to keep basic information and relations between objects,
- an on-disk repository where attachments and other files are stored. The
  `MySQL`_ database only keeps track on file locations and physically files are
  stored in this repository,
- `LibreOffice Base`_ project named **inventory.odb** -- its purpose is to
  provide user interface to the MySQL.


Installation
------------

Notes
-----

1. You must have macros enabled in your `LibreOffice Base`_ to use
   **inventory.odb**. My macros are not signed by trusted party so you probably
   have to change your macro safety settings to lower:
   **Tools | Options | Security | Macro Protection | Medium**
2. You must define you'r repository path in **config** table. Set the
   **repository_path** record in **config** table.
3. You probably also have to install **libreoffice-mysql-connector** package::

   apt-get install libreoffice-mysql-connector

Developer Notes
---------------

Cloning fresh repository:

.. code::

   git clone git@github.com:ptomulik/inventory
   cd inventory
   util/zip # this creates inventory.odb file out of inventory.d

Commiting changes made to ``inventory.odb``

.. code::

   util/unzip
   git add -A
   git commit -m 'commit message'


Updating database structure from running database:

.. code::

   mysqldump -u inventory -p inventory -d --single-transaction | sed 's/ AUTO_INCREMENT=[0-9]*//g' > inventory.sql

.. _LibreOffice Base: https://www.libreoffice.org/discover/base/
.. _MySQL: http://www.mysql.com/
