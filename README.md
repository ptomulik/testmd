# ptomulik-packagex

[![Build Status](https://travis-ci.org/ptomulik/puppet-packagex.png?branch=master)](https://travis-ci.org/ptomulik/puppet-packagex)

#### Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup](#setup)
    * [What packagex affects](#what-[modulename]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with packagex](#beginning-with-packagex)
4. [Usage](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Limitations](#limitations)
6. [Development](#development)

## Overview

This is an enhanced version of puppet *package* resource and some of its providers.

This is a place, where I develop and test my extended version of puppet *package* resource with enhanced providers. The releases found at https://forge.puppetlabs.com/ptomulik/packagex, are known to be functional and may be used with recent versions of puppet (3.2 and later). Currently I've developed *portsx* provider, which is an enhanced and bug-fixed version of puppet's *ports* provider.

## Module Description

The module re-implements puppet package resource adding some crucial features to it and its providers. Currently new features include:

  * *build_options* - a property (ensurable) that allows defining additional options for package. 
  * *portsx* provider - enhanced package provider for FreeBSD ports collection. It provides following new features and bug-fixes:
    * *install_options* - extra CLI flags passed to *portupgrade* when installing, reinstalling and upgrading packages,
    * *uninstall_options* - extra CLI flags passed to *pkg_deinstall* when uninstalling packages,
    * *build_options* - configuration options for package,
    * *portorigin*s (instead of *portname*s) are used to identify package instances,
    * it's upgradeable (tested, the original puppet provider declared that it's upgradeable, but it never worked for me),
    * *portversion* is used to find installed packages (instead of *pkg_info*),
    * *make search* is used to find uninstalled ports listed in puppet manifests,
    * failures when uninstalling packages due to dependency problems are no longer present. Now all packages that depend on the package in question get uninstalled as well (`pkg_deinstall -r`),
    * package conflicts when installing certain ports are no longer present. Original *ports* provider couldn't handle ports with common *portname* and sometimes it tried to install another port of an already installed package. This is no longer an issue, as we nativelly use *portorigin*s to identify packages.

The *build_options* are independent of the well known *install_options* and *uninstall_options*. The definition of *build_options* is generally provider-specific. Whereas *install_options* and *uninstall_options* are just parameters (not ensurable), the *build_options* is a fully featured property, that is the options may be ensured on a package. For *portsx* provider this is simply an `{OPTION => value}` hash, with boolean values. The *build_options* were added especially to allow *portsx* provider (FreeBSD ports) to define/ensure port options when installing and managing FreeBSD packages. Of course, the same concept may be reused by other providers, if necessary.

The *portsx* provider ensures that package is compiled with these *build_options*. Normally you would set build options with `make config` using ncurses-based frontend. Here, you can define *build_options* in your puppet manifest. If a package is already installed and you change its *build_options* in manifest file, the package gets rebuilt with new options and reinstalled.

Instead of *portname*s, *portorigin*s are used to identify *portsx* instances (see [FreeBSD ports collection and it's terminology](#freebsd-ports-collection-and-its-terminology)). This copes with several problems caused by portnames' ambiguity (see [FreeBSD ports collection and ambiguity of portnames](#freebsd-ports-collection-and-ambiguity-of-portnames)). You can now install and mainain ports that have common *portname* (but different portorigins). Examples of such packages include *mysql-client* or *ruby* (see below).

The [*portversion*](http://www.freebsd.org/cgi/man.cgi?query=portversion&manpath=ports&sektion=1) utility is used to find installed ports. It's better than using [*pkg_info*](http://www.freebsd.org/cgi/man.cgi?query=pkg_info&sektion=1) for several reasons. First, it is said to be faster, because it uses compiled version of ports INDEX file. Second, it works with both kinds of package databases - with the old *pkg* database and the new [*pkgng*](http://www.freebsd.org/doc/handbook/pkgng-intro.html) database, providing seamless interface to ony of them. Third, it provides package naemes and their "out-of-date" statuses in a single call, so we don't need to separatelly check out-of-date status for installed packages. While this version of *portsx* is not yet ready to work with *pkgng*, using *portversion* is a big step towards *pkgng* world.

#### FreeBSD ports collection and its terminology

We use the following terminology when referring ports/packages:

  * a string in form `'apache22'` or `'ruby'` is referred to as *portname* 
  * a string in form `'apache22-2.2.25'` or `'ruby-1.8.7.371,1'` is referred to
    as a *pkgname* 
  * a string in form `'www/apache22'` or `'lang/ruby18'` is referred to as a
    port *origin* or *portorigin*

See [http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html](http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html)

Port *origins* are used as primary identifiers for *portsx* instances. It's recommended to use *portorigins* instead of *portnames* as package names in manifest files.

#### FreeBSD ports collection and ambiguity of portnames

Using *portnames* (e.g. `apache22`) as package names in manifests is allowed. The *portname*s, however, are ambiguous, meaning that port search may find multiple ports matching the given *portname*. For example `'ruby'` package has three ports at the time of this writing  (2013-11-30): `mysql-client-5.1.71`, `mysql-client-5.5.33`, and `mysql-client-5.6.13` with origins `databases/mysql51-client`, `databases/mysql55-client` and `databases/mysql56-client` respectively. If none of these ports are installed and you use this ambiguous *portname* in your manifest, you'll se the following warning:

```
Warning: Puppet::Type::Packagex::ProviderPortsx: Found 3 ports named 'mysql-client': 'databases/mysql51-client', 'databases/mysql55-client', 'databases/mysql56-client'. Only 'databases/mysql56-client' will be ensured.
```


#### FreeBSD ports provider - resolved issues

##### Outdated ports installed when portorigin used in manifest

The test case is following (2013.11.30):

* package `mysql-client` is absent initially,
* there are three ports available: 
  * `databases/mysql51-client` (oldest),
  * `databases/mysql55-client`, and
  * `databases/mysql55-client` (most recent),
* the *site.pp* contains: `package {'mysql-client': ensure => present}` or `package {'mysql-client': ensure => latest}`,
* we run `puppet agent -t --debug --trace`

In output we observe:

```
...
Debug: Executing '/usr/local/sbin/portupgrade -N -M BATCH=yes mysql-client
...
```

after installation we have:

```
~ # portversion -v -o mysql-client
databases/mysql51-client    =  up-to-date with port
```

which is certainly not the most recent available version.

The same case, but with new *packagex* provider yields:
```
...
Warning: Puppet::Type::Packagex::ProviderPortsx: Found 3 ports named 'mysql-client': 'databases/mysql51-client', 'databases/mysql55-client', 'databases/mysql56-client'. Only 'databases/mysql56-client' will be ensured.
Debug: Executing '/usr/local/sbin/portupgrade -N -M BATCH=yes databases/mysql56-client'
...
```

on output and afer installation we have:

```
~ # portversion -v -o mysql-client
databases/mysql56-client    =  up-to-date with port 
```


## Setup

### What packagex affects

### Setup Requirements

You may need to enable **pluginsync** in your `puppet.conf`.

### Beginning with packagex

Using `package_options`:
                                                                                                                                                                                                                                                                               
    packagex{'apache22': package_options => {'SUEXEC' => true} }                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                               
## Usage                                                                                                                                                                                                                                                                       
                                                                                                                                                                                                                                                                               
## Reference                                                                                                                                                                                                                                                                   
                                                                                                                                                                                                                                                                               
## Limitations

* We don't support *pkgng* yet, but it shall be relativelly easy to add this support. The only things to be adapted are the `uninstall` method in provider class and the parts of code that determine `@property_hash[:build_options]` (*pkgng* stores options in package database, so we don't have to parse options files that may be out-of-sync with reality)
                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                               
## Development                                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                               
The project is held at github:                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                               
* [https://github.com/ptomulik/puppet-packagex](https://github.com/ptomulik/puppet-packagex)                                                                                                                                                                                   
                                                                                                                                                                                                                                                                               
Issue reports, patches, pull requests are welcome!                                                                                                                                                                                                                             
