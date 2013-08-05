#bsdportconfig

Configure build options for FreeBSD ports.

####Table of Contents

1. [Overview](#overview)
2. [Module Description - What the module does and why it is useful](#module-description)
3. [Setup - The basics of getting started with Bsdportconfig](#setup)
    * [What Bsdportconfig affects](#what-[apachex]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with Bsdportconfig](#beginning-with-Bsdportconfig)
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Limitations - OS compatibility, etc.](#limitations)
6. [Development - Guide for contributing to the module](#development)

##Overview

This module provides a **bsdportconfig** resource which ensures that certain
build options are set (or unset) for a given BSD port.

##Module Description

The **bsdportconfig** module helps to configure BSD ports. Installation and
deinstallaton of FreeBSD ports is handled very well by `package` resource
provided by core **puppet**, however there is no way to provide configuration
options for ports (no `"make config"` stage). This module helps to ensure, that
certain configuration options are set (or unset) for certain ports.

The module supports only the **on/off** options.

Source code is available at: https://github.com/ptomulik/puppet-bsdportconfig

##Setup

###What Bsdportconfig affects

This module affects:

* `$port_dbdir/*/options` where `$port_dbdir='/var/db/ports'` by default.

###Setup Requirements **OPTIONAL**

TODO:

If your module requires anything extra before setting up (pluginsync enabled, etc.), mention it here. 

###Beginning with Bsdportconfig

Ensure that 'www/apache22' is configured with LDAP and CGID modules:

    bsdportconfig {'www/apache22': options => { LDAP => on, CGID => on } }

Ensure that 'www/apache22' is configured without CGID module:

    bsdportconfig {'www/apache22': options => { CGID => off } }

Note, that the module changes only options listed in `options` parameter. Other
options are left unaltered (even if they currently differ from their default
values).

Install 'www/apache22' package with LDAP module enabled:

    bsdportconfig {'www/apache22': options => { LDAP => on }
    package { 'www/apache22': require => Bsdportconfig['www/apache22'] }

##Usage

###Defined types

####Defined type: bsdportconfig

**Parameters within `bsdportconfig`:**


#####`ensure`

Ensure that port configuration is synchronized with options. Accepts values:
insync, outofsync.

#####`name`

The package name. It has the same meaning and syntax as the $name parameter to
the package resource from core puppet (for the **ports** provider).

#####`options`

Options for the package. This is a hash with keys being option names and values
being on/off strings

#####`portsdir`

Location of the ports tree (absolute path). This is */usr/ports* on FreeBSD and
OpenBSD, and */usr/pkgsrc* on NetBSD.

#####`port_dbdir`

Directory where the result of configuring options are stored. Defaults to
*/var/db/ports*.

##Reference

TODO:

Here, list the classes, types, providers, facts, etc contained in your module. This section should include all of the under-the-hood workings of your module so people know what the module is touching on their system but don't need to mess with things. (We are working on automating this section!)

##Limitations

TODO:

This is where you list OS compatibility, version compatibility, etc.

##Development

TODO:

Since your module is awesome, other users will want to play with it. Let them know what the ground rules for contributing are.

##Release Notes/Contributors/Etc **Optional**

TODO:

If you aren't using changelog, put your release notes here (though you should consider using changelog). You may also add any additional sections you feel are necessary or important to include here. Please use the `## ` header. 
