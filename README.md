# ptomulik-packagex

#### Table of Contents

1. [Overview](#overview)
2. [Module Description - What the module does and why it is useful](#module-description)
3. [Setup - The basics of getting started with [Modulename]](#setup)
    * [What [Modulename] affects](#what-[modulename]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with [Modulename]](#beginning-with-[Modulename])
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

The `packagex` is a defined type which wraps the core `package` resource. 
It adds some features to the core `package`, including:

* expressions in `ensure` parameter (e.g. `ensure => '>=2.2.4'`) - this enables
  an extended versioning (also on package providers that are not versionable by
  their own),
* passing an array of (equivalent) package names to let the `packagex` pickup
  first available candidate for installation,
* passing build options to package managers which compile/build their packages
  before installation (FreeBSD ports, for example)

The `packagex` defined type works mostly as the core `package` resource. It
accepts all the parameters known to the core `package` resource plus parameters
that extend its functionality. Defaults for the `package` resource are honored.

In addition, the `packagex` module provides a `packagex_defaultprovider` fact, 
which tells to the master, what is the default package provider for the target
(agent) OS.

## Setup

### What [Modulename] affects

* A list of files, packages, services, or operations that the module will alter, impact, or execute on the system it's installed on.
* This is a great place to stick any warnings.
* Can be in list or paragraph form. 

### Setup Requirements **OPTIONAL**

You may need to enable **pluginsync** in your `puppet.conf`.

### Beginning with [Modulename]

The very basic steps needed for a user to get the module up and running. 

If your most recent release breaks compatibility or requires particular steps for upgrading, you may wish to include an additional section here: Upgrading (For an example, see http://forge.puppetlabs.com/puppetlabs/firewall).

## Usage

Put the classes, types, and resources for customizing, configuring, and doing the fancy stuff with your module here. 

## Reference

Here, list the classes, types, providers, facts, etc contained in your module. This section should include all of the under-the-hood workings of your module so people know what the module is touching on their system but don't need to mess with things. (We are working on automating this section!)

## Limitations

The `packagex` module has the following limitations:

* the `$build_options` are currently supported only by the `ports` provider,
* packages are not rebuilt/reinstalled automatically when only `$build_options`
  get changed; if you need to change build options for a package, you should
  deinstall it before compiling the modified puppet catalog,

## Development

Since your module is awesome, other users will want to play with it. Let them know what the ground rules for contributing are.

## Release Notes/Contributors/Etc **Optional**

If you aren't using changelog, put your release notes here (though you should consider using changelog). You may also add any additional sections you feel are necessary or important to include here. Please use the `## ` header. 
