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

Facter types/providers simplify gathering data from different sources. An
example of such a data is a list of packages available for installation. This
information may be represented in a common form, but we must first obtain
it from a specific sources such as apt, yum, zypper, freebsd ports, etc.. Each
of these commands (backends) needs to be invoked in a specific manner, and we
need a command-specific parser to interpret its output. The variety of backends
leads to the idea of data provider object responsible for data acquisition and
unification.

In general, the following top-level aspects of information acquisition may be
identified: 

1. Retrieving (prefetching) data from a backend command.
2. Extracting particular information from the results prefetched in 1..


The data retrieval (aspect 1.) involves the following steps: 

1. Defining a *query* in a backend-independent DSL. We assume, that each fact
   may define one or more such a queries. The queries are categorized into
   *types*, and the parameters that comprise the query are specific to the
   given type.
2. *Validating the query* to prevent user's mistakes.
3. *Munging the query*:
  - provider-specific munging which involves:
    - fact-and-provider-specific munging customizable by user.
4. *Merging* user-defined queries *of a given type* to retrieve data required
   by multiple facts with a single call to the backend command (if possible).
5. *Preparing command line string* used to invoke the backend command.
6. *Invoking* the command.
7. *Parsing* command output and caching the parsed results.

The result extraction (aspect 2.) involves the following steps:

1. Defining a *filter* to extract particular information from the prefetched
   results.
2. *Munging the filter*:
   - provider-specific munging, which involves:
     - fact-and-provider-specific munging customizable by user.
3. *Filtering the prefetched results* to return only what's required by the
   user.
4. *Postprocessing the results*:
   - provider-specific postprocessing, which involves:
     - fact-and-provider-specific postprocessing customizable by user.

This module defines a DSL to implement new types and providers and a recipe to
use them. The DSL allows one to define/customize the following aspects of data
retrieval:

- defining queries,
- validating queries,
- munging queries,
  - provider specific munging of the query,
  - fact-specific munging of the query,
- merging queries into one collective query,
- preparing command used to perform the merged query,
- invoking command to prefetch information requested in queries,
- parsing command output,
- caching the parsed result,
- retrieving the cached result (filtering),
- 

[[Table of Contents](#table-of-contents)]

###<a id="validating-query">

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
