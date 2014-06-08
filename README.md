#ptomulik-facter\_types

[![Build Status](https://travis-ci.org/ptomulik/facter-types.png?branch=master)](https://travis-ci.org/ptomulik/facter-types)
[![Coverage Status](https://coveralls.io/repox/ptomulik/facter-types/badge.png)](https://coveralls.io/r/ptomulik/facter-types)
[![Code Climate](https://codeclimate.com/github/ptomulik/facter-types.png)](https://codeclimate.com/github/ptomulik/facter-types)

####<a id="table-of-contents"></a>Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Usage](#usage)
   * [Example 1: Define new type](#example-1-define-new-type)
4. [Reference](#reference)
   * [Function Reference](#function-reference)
   * [API Reference](#api-reference)
5. [Limitations](#limitations)

##<a id="overview"></a>Overview

Pluggable facter types and providers. 

[[Table of Contents](#table-of-contents)]

##<a id="module-description"></a>Module Description

Facter types/providers simplify gathering data from specific sources. An
example of such a data is a list of packages available for installation. This
information may be represented in a predefined format, but we must first obtain
it from a specific sources such as apt, yum, zypper, freebsd ports, etc.. Each
of these commands (backends) needs to be invoked in a specific manner, and we
need a command-specific parser to interpret its output. The variety of backends
leads to the idea of data provider object responsible for data acquisition and
unification.

Facter types and providers are quite similar to puppet resource types and
providers. We have a specific DSL to implement new types and providers and a
recipe to use them. The sole difference, however, is that whereas the puppet
types manage particular resources, the facter types just retrieve data.


[[Table of Contents](#table-of-contents)]

##<a id="usage"></a>Usage

###<a id="example-1-define-new-type"></a>Example 1: Define new type

In this example we define new type named ``package``. Create a file
``lib/facter/type/package.rb`` with the following content

```ruby
require 'facter/type'
Facter::Type.newtype 'package' do
  # code for your new 'package' class goes here.
end
```

[[Table of Contents](#table-of-contents)]

##<a id="reference"></a>Reference

###<a id="api-reference"></a>API Reference

API reference may be generated with

```console
bundle exec rake yard
```

The generated documentation goes to `doc/` directory. Note that this works only
under ruby >= 1.9.

[[Table of Contents](#table-of-contents)]

##Limitations

[[Table of Contents](#table-of-contents)]
