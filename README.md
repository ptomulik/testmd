#bsdportconfig

Configure build options for FreeBSD ports.

Source code is available at: [https://github.com/ptomulik/puppet-bsdportconfig](https://github.com/ptomulik/puppet-bsdportconfig)

####Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup](#setup)
    * [What Bsdportconfig affects](#what-bsdportconfig-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with Bsdportconfig](#beginning-with-bsdportconfig)
4. [Usage](#usage)
5. [Limitations](#limitations)
6. [Development](#development)

##Overview

This module provides a **bsdportconfig** resource which ensures that certain
build options are set (or unset) for a given BSD port.

##Module Description

The **bsdportconfig** module helps to configure BSD ports.

Installation and de-installation of FreeBSD ports is handled quite well by
`package` resource provided by core **puppet**. However, it provides no way to
set configuration options for ports (no `"make config"` stage), and all
installs packages with their default configurations (or with manually pre-set
options).

This module tries to fill this gap. It helps to ensure, that certain
configuration options are set (or unset) for certain ports. You may chain the
**bsdportconfig** resource with **package** to achieve automatic configuration
of ports before they get installed.

The module supports only the **on/off** options.

##Setup

###What Bsdportconfig affects

This module affects:

* config options for given ports, it's done by modifying options files
  `$port_dbdir/*/options`, where `$port_dbdir='/var/db/ports'` by default.

###Setup Requirements

You may need to enable **pluginsync** in your *puppet.conf*.

###Beginning with Bsdportconfig

**Note**: the resource modifies only the options listed in `options`
parameter. Other options are left unaltered (even if they currently differ from
their default values defined by port's Makefile).


*Example*:

Ensure that 'www/apache22' is configured with SUEXEC and CGID modules:

    bsdportconfig {'www/apache22': options => { 'SUEXEC' => on, 'CGID' => on } }

*Example*:

Ensure that 'www/apache22' is configured without CGID module:

    bsdportconfig {'www/apache22': options => { 'CGID' => off } }

*Example*:

Install 'www/apache22' package with LDAP module enabled:

    bsdportconfig {'www/apache22': options => { 'LDAP' => on }
    package { 'www/apache22': require => Bsdportconfig['www/apache22'] }

##Usage

###Resource type: `bsdportconfig`

####Parameters within `bsdportconfig:

#####`ensure` (optional)

Ensure that port configuration is synchronized with the resource. Accepts
value: `insync`. Defaults to `insync`. 

#####`name` (required)

The package name. It has the same meaning and syntax as the `$name` parameter
to the **package** resource from core puppet (for the **ports** provider).

#####`options` (optional)

Options for the package. This is a hash with keys being option names and values
being `'on'`/`'off`' strings. Defaults to an empty hash.

#####`portsdir` (optional)

Location of the ports tree (absolute path). This is */usr/ports* on FreeBSD and
OpenBSD, and */usr/pkgsrc* on NetBSD. 

#####`port_dbdir` (optional)

Directory where the result of configuring options are stored. Defaults to
*/var/db/ports*.

##Limitations

Currently tested on FreeBSD only.

##Development

Project is held on GitHub:

[https://github.com/ptomulik/puppet-bsdportconfig](https://github.com/ptomulik/puppet-bsdportconfig)

Feel free to submit issue reports to issue tracker or create pull requests.
