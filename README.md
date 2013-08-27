# ptomulik-packagex

#### Table of Contents

1. [Overview](#overview)
2. [Module Description - What the module does and why it is useful](#module-description)
3. [Setup - The basics of getting started with packagex](#setup)
    * [What packagex affects](#what-[modulename]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with packagex](#beginning-with-packagex)
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Limitations - OS compatibility, etc.](#limitations)
6. [Development - Guide for contributing to the module](#development)

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

* A list of files, packages, services, or operations that the module will alter, impact, or execute on the system it's installed on.
* This is a great place to stick any warnings.
* Can be in list or paragraph form. 

### Setup Requirements **OPTIONAL**

You may need to enable **pluginsync** in your `puppet.conf`.

### Beginning with packagex

The very basic steps needed for a user to get the module up and running. 

If your most recent release breaks compatibility or requires particular steps for upgrading, you may wish to include an additional section here: Upgrading (For an example, see http://forge.puppetlabs.com/puppetlabs/firewall).

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

Since your module is awesome, other users will want to play with it. Let them know what the ground rules for contributing are.

## Release Notes/Contributors/Etc **Optional**

If you aren't using changelog, put your release notes here (though you should consider using changelog). You may also add any additional sections you feel are necessary or important to include here. Please use the `## ` header. 
