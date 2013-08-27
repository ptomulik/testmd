# ptomulik-packagex

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

This module implements a `packagex` defined type which adds some features to
the standard `package` resource. For example, a simple versioning may be
enabled for providers which are not versionable by their own (FreeBSD ports
for example). Other features are available as well, and are described in
subsequent sections.

## Module Description

The module implements a `packagex` defined type, which wraps the core `package`
resource. It adds some features to the core `package`, including:

* version expressions in `ensure` parameter (e.g. `ensure => '<2.4.0'`) - this
  enables an extended versioning, 
* passing arrays of package names and letting the `packagex` to install first
  available candidate,
* passing build options to package managers which compile/build their packages
  (FreeBSD ports, for example)

The `packagex` defined type works mostly as the core `package` resource. It
accepts all the parameters known to the core `package` resource plus parameters
that extend its functionality. Defaults for the `package` resource are honored.

In addition, the `packagex` module provides the following facts:

* `packagex_defaultprovider` - tells what is the default package provider for
  the target (agent) OS.

## Setup

### What packagex affects

Since the `packagex` is merely a wrapper around the `package` resource, it
affects all the things the core `package` affects. In addition, the following
subjects may be altered on your system:

* a debug file(s) may be generated, if requested (see the `$debugfile`
  parameter)
* option files for FreeBSD ports may be altered, see the `$build_options`
  parameter and documentation of
  [ptomulik-bsdportconfig](https://forge.puppetlabs.com/ptomulik/bsdportconfig)
  module,

### Setup Requirements

You may need to enable **pluginsync** in your `puppet.conf`.

### Beginning with packagex

Let's start with a trivial example. To install `apache2` package, we simply do

    packagex {'apache2': }

It has exactly same effect as `package{'apache2': }`, nothing special yet.

Now imagine, you have a puppet module which configures the apache http server.
It works well with older versions but it's not ready for the apache 2.4 yet. 
Your **apt** repositories provide versions `2.2.22-13` and `2.4.6-3` for
installation. By default, the latest version (`2.4.6-3`) gets installed. 
To install version < 2.4 of a package we may use version expression feature of
the `packagex` defined type:

    packagex {'apache2':
      ensure     => '< 2.4.0',
      versions   => { apt => {apache2 => ['2.2.22-13','2.4.6-3']} },
      candidates => { apt => {apache2 => '2.4.6-3'} },
      installed  => { },  # see later ...
    }

Consider similar case on FreeBSD, where **ports** are used to install packages. 
Assume, there are the following packages: `apache22` (ver. `2.2.25`) and
`apache24` (ver. `2.4.6`). In this case, we may use `packagex` to install 
apache < 2.4, as follows

    packagex {'apache2':
      name       => ['apache24', 'apache22'],
      ensure     => '< 2.4.0',
      versions   => { ports => {apache22=>['2.2.25'], apache24=>['2.4.6']} },
      candidates => { ports => {apache22=>'2.2.25', apache24=>'2.4.6'} },
      installed  => { },  # see later ...
    }

With the above syntax, `packagex` selects for installation one of the packages
listed in `name` array. What is selected for installation depends on the
available package versions (the `versions` parameter), the user requirements
prescribed with version expression (the `ensure` parameter) and on the order in
which the package names appear in `name` parameter (packages from the left are
considered to be preferred). If there are still multiple versions suitable for
installation, the most recent version is selected.

The main question is what to put into the `name`, `versions`, `candidates` and
`installed` parameters. Their values may be generated automatically at agent
side and passed to the master as facts. We may use a
[ptomulik-repoutil](https://forge.puppetlabs.com/ptomulik/repoutil) plugin to
fill-up some facts with data for `versions`  and `candidates`. For this
example, we'll implement three facts: an `apache_repo_versions` fact,
an `apache_repo_candidates` fact, and an `apache_installed` fact.

First fact, the `apache_repo_versions`, tells us what versions of apache
packages are available for installation:

    # lib/facter/apache_repo_versions.rb
    require 'puppet/util/repoutil'
    Facter.add(:apache_repo_versions, :timeout => 600) do
      setcode do
        names = ['apache2', 'apache22', 'apache24' ]
        Puppet::Util::RepoUtils.package_versions(names).to_pson
      end
    end

Second fact, the `apache_repo_candidates`, tells what versions of apache
packages are installation candidates:

    # lib/facter/apache_repo_candidates.rb
    require 'puppet/util/repoutil'
    Facter.add(:apache_repo_candidates, :timeout => 600) do
      setcode do
        names = ['apache2', 'apache22', 'apache24' ]
        Puppet::Util::RepoUtils.package_candidates(names).to_pson
      end
    end

Third fact, the `apache_installed`, tells us what apache packages are currently
installed on the agent

    # lib/facter/apache_installed.rb
    Facter.add(:apache_installed, :timeout => 600) do
      setcode do
        installed = {}
        names = ['apache2', 'apache22', 'apache24']
        names.each do |name|
          package = Puppet::Resource::indirection.find("package/#{name}")
          if package.is_a?(Puppet::Resource) and package[:ensure] =~ /^[0-9]+\./
            installed[name] = package[:ensure]
          end
        end
        installed.to_pson
      end
    end

Now, things become a little bit easier. To install apache < 2.4, we do:

    packagex {'apache2':
      names     => ['apache2', 'apache22', 'apache24'],
      ensure    => '< 2.4.0',
      versions  => $::apache_repo_versions,
      candidates  => $::apache_repo_candidates,
      installed => $::apache_installed,
    }

Once, your module becomes ready for apache 2.4, you may change the 
`ensure => '< 2.4.0'` to `ensure => '~= 2.4'`, to ensure install apache 2.4.


Obviously, we may pass all the parameters `package` supports and they will have 
same effect as for the core `package` resource. For the full list of
`package`'s parameters see the [package
reference](https://docs.puppetlabs.com/references/latest/type.html#package).

Consider the following situation. Your repository contains several packages
that provide *apache2* http server. For example, the FreeBSD ports include (at
the time of this writing) the following packages:

* `apache22` - version `2.2.25`, prefork MPM,
* `apache22-event-mpm` - version `2.2.25`, event MPM,
* `apache22-itk-mpm` - version `2.2.25`, itk MPM,
* `apache22-peruser-mpm` - version `2.2.25`, peruser MPM,
* `apache22-worker-mpm` - version `2.2.25`, worker MPM,
* `apache24` - version `2.4.6`, MPM as DSO or selected as compile option.

At the same time, Debian repositories provide the following packages:

* `apache2` - versions: `2.2.22-13` (worker MPM) and `2.4.6-3` (MPMs as DSO),
* `apache2-mpm-event` - versions: `2.2.22-13` (worker MPM) and `2.4.6-3` (MPMs as DSO),
* `apache2-mpm-itk` - versions: `2.2.22-13` (worker MPM) and `2.4.6-3` (MPMs as DSO),
* `apache2-mpm-prefork` - versions: `2.2.22-13` (worker MPM) and `2.4.6-3` (MPMs as DSO),
* `apache2-mpm-worker` - versions: `2.2.22-13` (worker MPM) and `2.4.6-3` (MPMs as DSO),

You develop puppet classes which install and configure apache http server.
One of them, let say `apachex::package`, is responsible for the installation.
Apache configuration options change from version to version and some of them
differ significantly between versions `2.2` and `2.4`. It should be known in
advance which version is going to be installed in order to select appropriate
templates for configuration files. In addition, the following aspects must be
considered before installing the package

* for apache < 2.4 MPM must be selected by selecting appropriate package, 
  for >= 2.4 MPMs are available as dynamic modules, and default MPM is choosen
  at compile time (via build options) - again: some compile-time options must 
  be set on FreeBSD ports,
* most of the apache modules are part of the apache project; on Debian most of
  them are available as separate installable packages; on FreeBSD (ports),
  however, you must set appropriate options (`make config`) for an appropriate
  apache port to have your modules installed,

So, You give the user an option to choose
between `2.2`, `2.4` (or, more generally, to use `2.X`). In addition, the
`apachex::package` needs information about the (default) MPM to be used and few
other parameters. Let say, you arrive at the following class interface:

    class apachex::package(
      $version,
      $mpm,
      $modules,
    ) {
    # ...
    }

The module should be used together with the `ptomulik-repoutil`. It's included
in the list of dependencies, and shall install automatically when the
`ptomulik-packagex` gets installed.



## Usage

Put the classes, types, and resources for customizing, configuring, and doing the fancy stuff with your module here. 

## Reference

Here, list the classes, types, providers, facts, etc contained in your module. This section should include all of the under-the-hood workings of your module so people know what the module is touching on their system but don't need to mess with things. (We are working on automating this section!)

## Limitations

The `packagex` module has certain limitations, for example:

* the `$build_options` are currently supported only by the `ports` provider,
* packages are not rebuilt/reinstalled automatically when only `$build_options`
  get changed; if you need to change build options for a package, you should
  deinstall it before compiling the modified puppet catalog,
* version expressions and the resulting extended versioning is not a perfect
  solution (and will never be probably). It's possible, for example, that the
  package lists and their versions passed from agents to `packagex` (via facts)
  are outdated. This may cause failures when puppet start to install requested
  packages (when the catalog is already compiled). The problem may persist even
  if an appropriate cache refresh command (`apt-get update`, for example) is
  invoked systematically by puppet. The facts are always generated before the
  cache is refreshed.


## Development

The project is held at github:

* [https://github.com/ptomulik/puppet-packagex](https://github.com/ptomulik/puppet-packagex)

Issue reports, patches, pull requests are welcome!
