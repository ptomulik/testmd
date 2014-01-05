# ptomulik-portsxutil

[![Build Status](https://travis-ci.org/ptomulik/puppet-portsxutil.png?branch=master)](https://travis-ci.org/ptomulik/puppet-portsxutil)
[![Coverage Status](https://coveralls.io/repos/ptomulik/puppet-portsxutil/badge.png?branch=master)](https://coveralls.io/r/ptomulik/puppet-portsxutil?branch=master)
[![Code Climate](https://codeclimate.com/github/ptomulik/puppet-portsxutil.png)](https://codeclimate.com/github/ptomulik/puppet-portsxutil)

#### Table of Contents

0. [Caution](#caution)
1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup](#setup)
    * [What portsxutil affects](#what-portsxutil-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with portsxutil](#beginning-with-portsxutil)
4. [Usage](#usage)
5. [Reference](#reference)
6. [Limitations](#limitations)
7. [Development](#development)

## Caution

This is an experimental module. It may be substantially changed, renamed or
removed at all without a notice. Do not use in production.

## Overview

This library contains extension modules for searching FreeBSD
[ports(7)](http://www.freebsd.org/cgi/man.cgi?query=ports&sektion=7) and
packages. It uses `make search` command to search available ports and
[portversion(1)](http://www.freebsd.org/cgi/man.cgi?query=portversion&manpath=ports&sektion=1)
to query already installed packages. Both, the old
[pkg](http://www.freebsd.org/doc/handbook/packages-using.html) and the new
[pkgng](http://www.freebsd.org/doc/handbook/pkgng-intro.html) databases are
supported. The library also allows manipulating build options (normally set
with *make config*) and determining other characteristics of FreeBSD packaging
system.

The library is developed primarily for
[ptomulik-packagex\_portsx](https://github.com/ptomulik/puppet-packagex_portsx)
module, which implements [ports](https://www.freebsd.org/ports/) provider for
[packagex](https://github.com/ptomulik/puppet-packagex) resource.

## Module Description

The library contains the following ruby modules:

- `Puppet::Util::PTomulik::Packagex::Portsx::Functions` - common stuff used by
  other modules,
- `Puppet::Util::PTomulik::Packagex::Portsx::Options` - maintains FreeBSD ports
  options (build options, normally settable by `make search`),
- `Puppet::Util::PTomulik::Packagex::Portsx::PkgRecord` - represents single
  record from package database (database of installed packages, either pkg or
  pkgng),
- `Puppet::Util::PTomulik::Packagex::Portsx::PkgSearch` - methods for searching
  database of installed packages,
- `Puppet::Util::PTomulik::Packagex::Portsx::PortSearch` - methods for
  searching ports INDEX,
- `Puppet::Util::PTomulik::Packagex::Portsx::PortRecord` - represents single
  record from ports INDEX,
- `Puppet::Util::PTomulik::Packagex::Portsx::Record` - superclass for PkgRecord
  and PortRecotr


#### FreeBSD ports collection and its terminology

Ports and packages in FreeBSD may be identified by either *portnames*,
*pkgnames* or *portorigins*. We use the following terminology when referring
ports/packages:

  * a string in form `'apache22'` or `'ruby'` is referred to as *portname*
  * a string in form `'apache22-2.2.25'` or `'ruby-1.8.7.371,1'` is referred to
    as a *pkgname*
  * a string in form `'www/apache22'` or `'lang/ruby18'` is referred to as a
    port *origin* or *portorigin*

See [http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html](http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html)

## Setup

### What portsxutil affects

This is just a library and shouldn't alter your system (it performs read-only
operations). The module uses Facter and some *read-only* shell commands to
query information related to FreeBSD ports/packages.

### Setup Requirements

You may need to enable **pluginsync** in your `puppet.conf`.

### Beginning with portsxutil

## Usage

## Reference

The complete reference may be generated with [yard](http://yardoc.org/) as
follows:

```bash
yard -m markdown
```

## Limitations

## Development
The project is held at github:
* [https://github.com/ptomulik/puppet-portsxutil](https://github.com/ptomulik/puppet-portsxutil)
Issue reports, patches, pull requests are welcome!
