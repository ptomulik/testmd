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

Enhanced version of package resource type and some providers.

This is a place, where I develop and test my extended version of puppet *package* resource with enhanced providers. I hope that most of the stuff found here will be good enough to be merged into puppet core soon.

Despite this project is a kind of sandpit, the releases found at https://forge.puppetlabs.com/ptomulik/packagex are known to be functional and may be used recent versions of puppet.

Currently I experiment with packagex/portsx provider, which is an enhanced and bug-fixed version of puppet's package/ports package provider.

## Module Description

The module re-implements puppet package resource adding some features to it and its providers. Currently new features include:

  * *build_options* - a property (ensurable) that allows defining additional options for package. 
  * *portsx* provider - enhanced and bug-fixed package provider for FreeBSD ports collection. It provides following features:
    * *install_options* - extra CLI flags passed to *portupgrade* when installing, reinstalling and upgrading packages,
    * *uninstall_options* - extra CLI flags passed to *pkg_deinstall* when uninstalling packages,
    * *build_options* - configuration options for package,
    * portorigins (instead of portnames) used to identify resource instances,
    * it's upgradeable (tested, the original ports provider seemed to just declare that it's upgradeable, but it didn't work for me),
    * 


The *build_options* are independent of *install_options* and *uninstall_options*. The definition of *build_options* is generally provider-specific. For *portsx* this is simply an `{OPTION => value}` hash, with boolean values. The *build_options* were added especially to allow *portsx* provider (FreeBSD ports) to define/ensure port options when installing and managing FreeBSD packages

The *portsx* provider ensures that package is compiled with these options (normally you set them with `make config` or write them manually `/var/db/ports/some_port/options` file). If a package is already installed and you change its *build_options* in manifest file, the package gets rebuilt with new options and reinstalled.

Portorigins are used as resource names instead of portnames (see [FreeBSD ports collection and it's terminology](#freebsd-ports-collection-and-its-terminology)). This copes with several problems caused by portnames' ambiguity.

#### FreeBSD ports collection and it's terminology

We use the following terminology when referring ports/packages:

  * a string in form `'apache22'` or `'ruby'` is referred to as *portname* 
  * a string in form `'apache22-2.2.25'` or `'ruby-1.8.7.371,1'` is referred to
    as a *pkgname* 
  * a string in form `'www/apache22'` or `'lang/ruby18'` is referred to as a
    port *origin* 

See [http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html](http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html)

Port *origins* are used as primary identifiers for bsdportconfig instances.
It's recommended to use *origins* or *pkgnames* to identify ports.

#### FreeBSD ports collection and ambiguity of portnames

Accepting *portnames* (e.g. `apache22`) as the [name](#name-required)
parameter was introduced for convenience in 0.2.0. However, *portnames* in
this form are ambiguous, meaning that port search may find multiple ports 
matching the given *portname*. For example `'ruby'` package has three ports
at the time of this writing  (2013-08-30): `ruby-1.8.7.371,1`,
`ruby-1.9.3.448,1`, and `ruby-2.0.0.195_1,1` with origins `lang/ruby18`,
`lang/ruby19` and `lang/ruby20` respectively. If you pass a portname which
matches multiple ports, transaction will fail with a message such as:

#### FreeBSD ports provider - resolved issues



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
                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                               
## Development                                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                               
The project is held at github:                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                               
* [https://github.com/ptomulik/puppet-packagex](https://github.com/ptomulik/puppet-packagex)                                                                                                                                                                                   
                                                                                                                                                                                                                                                                               
Issue reports, patches, pull requests are welcome!                                                                                                                                                                                                                             
