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
- `LibreOffice Base`_ project named **inventory.odb** -- it's purpose is to
  provide user interface to the MySQL.


Installation
------------

Notes
-----

1. You must have macros enabled in your `LibreOffice Base`_ to use
   **inventory.odb**. My macros are not signed by trusted party so you probably
   have to change your macro safety settings to lower.

- **Tools | Options | Security | Macro Protection | Medium**

.. _LibreOffice Base: https://www.libreoffice.org/discover/base/
.. _MySQL: http://www.mysql.com/
