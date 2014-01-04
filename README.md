# ptomulik-portsxutil

[![Build Status](https://travis-ci.org/ptomulik/puppet-portsxutil.png?branch=master)](https://travis-ci.org/ptomulik/puppet-portsxutil)
[![Coverage Status](https://coveralls.io/repos/ptomulik/puppet-portsxutil/badge.png?branch=master)](https://coveralls.io/r/ptomulik/puppet-portsxutil?branch=master)
[![Code Climate](https://codeclimate.com/github/ptomulik/puppet-portsxutil.png)](https://codeclimate.com/github/ptomulik/puppet-portsxutil)

#### Table of Contents

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

## Overview

__NOTE__: this is an experimental module. It may be substantially changed, renamed or removed at all without a notice. Do not use in production.

This library provides functions for searching through FreeBSD [ports(7)](http://www.freebsd.org/cgi/man.cgi?query=ports&sektion=7) and installed packages. It uses `make search` command to search through available ports and [portversion(1)](http://www.freebsd.org/cgi/man.cgi?query=portversion&manpath=ports&sektion=1) to query already installed packages. Both, the old [pkg](http://www.freebsd.org/doc/handbook/packages-using.html) and the new [pkgng](http://www.freebsd.org/doc/handbook/pkgng-intro.html) databases are supported. The library also allows manipulating build options (the options normally set with *make config*).

The library is developed primarily for [ptomulik-packagex\_portsx](https://github.com/ptomulik/puppet-packagex_portsx) module, which implements [ports](https://www.freebsd.org/ports/) provider for [packagex](https://github.com/ptomulik/puppet-packagex) resource.

## Module Description                                                                     
                                                                                          
This module provides a                                                                    
                                                                                          
#### FreeBSD ports collection and its terminology                                         
                                                                                          
We use the following terminology when referring ports/packages:                           
                                                                                          
  * a string in form `'apache22'` or `'ruby'` is referred to as *portname*                
  * a string in form `'apache22-2.2.25'` or `'ruby-1.8.7.371,1'` is referred to           
    as a *pkgname*                                                                        
  * a string in form `'www/apache22'` or `'lang/ruby18'` is referred to as a              
    port *origin* or *portorigin*                                                         
                                                                                          
See [http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html](http://www.freebsd.org/doc/en/books/porters-handbook/makefile-naming.html)

#### FreeBSD ports collection and ambiguity of portnames

The *portnames* are ambiguous which means a port search may find multiple ports
matching a single *portname*. For example, at some time it was that
`'mysql-client'` package had three ports: with origins
`databases/mysql51-client`, `databases/mysql55-client` and
`databases/mysql56-client`. This may cause some problems to applications using
this library, so youre encouraged to use port origins (instead of port names).

## Setup

### What portsxutil affects

This is just a library and shouldn't touch anything in your system when
installed. The operations provided by 

### Setup Requirements

You may need to enable **pluginsync** in your `puppet.conf`.

### Beginning with portsxutil

## Usage

## Reference

## Limitations

## Development
The project is held at github:
* [https://github.com/ptomulik/puppet-portsxutil](https://github.com/ptomulik/puppet-portsxutil)
Issue reports, patches, pull requests are welcome!
