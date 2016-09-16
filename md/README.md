# ptomulik-backports

[![Build Status](https://travis-ci.org/ptomulik/puppet-backports.png?branch=master)](https://travis-ci.org/ptomulik/puppet-backports)

#### Table of Contents

1. [Overview](#overview)
2. [Setup](#setup)
    * [Setup requirements](#setup-requirements)
3. [Development](#development)

## Overview

Backport selected features to older versions of Puppet.

## Setup

### Setup Requirements

You must enable **pluginsync** in your `puppet.conf`.

### Beginning with backports

It's just enough to `require` appropriate file from this module to backport
certain feature. For example, to backport a `package_settings` (a feature and
property of the `package` type introduced in Puppet 3.5.0) put the following
line in an appropriate place (before code that relies on this feature)

    require 'puppet/backport/type/package/package_settings'

### Features

|           Feature                |                       How to backport                         |
|----------------------------------|---------------------------------------------------------------|
| `type/package/package_settings`  | `require 'puppet/backport/type/package/package_settings'`     |
| `type/package/uninstall_options` | `require 'puppet/backport/type/package/uninstall_options'`    |

## Development
The project is held at github:
* [https://github.com/ptomulik/puppet-backports](https://github.com/ptomulik/puppet-backports)
Issue reports, patches, pull requests are welcome!
