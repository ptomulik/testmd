# ptomulik-vash

[![Build Status](https://travis-ci.org/ptomulik/puppet-vash.png?branch=master)](https://travis-ci.org/ptomulik/puppet-vash)

#### Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup](#setup)
    * [What vash affects](#what-[modulename]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with vash](#beginning-with-vash)
4. [Usage](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Limitations](#limitations)
6. [Development](#development)

## Overview

Hash mixin with simple validation and munging.

# Module Description

Vash provides mixins that add Hash interface to classes. The mixins allow you
to enable simple data validation and munging, such that you may define
restrictions on keys, values and pairs entering your hash and coalesce them
at input.

## Setup

### Setup Requirements

You may need to enable pluginsync in your `puppet.conf`.

### Beginning with vash

There are two patterns for addign Vash functionality to your class. The first
one is to use `Vash::Contained` mixin, as follows

```ruby
require 'puppet/util/ptomulik/vash/contained'
class MyVash
  include Puppet::Util::PTomulik::Vash::Contained
end
```

The second pattern is to use `Vash::Inherited`

```ruby
require 'puppet/util/ptomulik/vash/inherited'
class MyVash < Hash
  include Puppet::Util::PTomulik::Vash::Inherited
end
```

With the first pattern, hash data is keept in an instance variable
`@vash_underlying_hash`. With second pattern the superclass of `MyVash` keeps
hash data and it should already have `Hash` functionality (`Vash` only adds
input validation and munging).

Once you have included `Vash::Contained` or `Vash::Inherited` module to your
class, you may use it as ordinary Hash:

```ruby
vash = MyVash[ [[:a,:A],[:b,:B]] ]
vash[:c] = :C
# .. and so on
```

There is no validation nor munging by default, so `MyVash` behaves exactly same
way as `Hash`, accepting any input data and not modifying it.

Simple validation may be added by defining `vash_valid_key?`,
`vash_valid_value?` and `vash_valid_pair?` methods (note, all methods that are
specific to Vash, have `vash_` prefix):

```ruby
require 'puppet/util/ptomulik/vash/contained'
class MyVash
  include Puppet::Util::PTomulik::Vash::Contained
  # accept only integers as keys
  def vash_valid_key?(key); true if Integer(key) rescue false; end
end
```

```ruby
vash = MyVash[1,2]
# => {1=>2}
vash[2] = 3
# => 3
vash
# => {1=>2, 2=>3}
vash['a'] = 1
# InvalidKeyError: invalid key "a"
```

Restrictions may further be defined for values and pairs. The following
subsections shall give more detailed explanations.

## Usage

The main purpose of `Vash` mixins is to extend basic `Hash` functionality
adding validation and munging of input data. When new data enters `Vash`,
the workflow is following:

1. Input items are passed to `#vash_validate_item` (the term *item* is used
   for original `[key,value]` pair as entered by user).
2. The key and value are validated separately by `#vash_validate_key` and
   `#vash_validate_value`. These methods call `#vash_valid_key?` and
   `#vash_valid_value?` to ask, if the key and value may be further
   processed.
3. If key and value are acceptable, the `#vash_munge_key` and
   `#vash_munge_value` are called to perform optional data munging.
   The `#vash_validate_key` and `#vash_validate_value` return munged key and
   value.
4. The munged `[key,value]` pair is referred to as *pair*. It is passed to
   `#vash_validate_pair` in order to ensure, that it satisfies pair
   restrictions. The `#vash_validate_pair` asks `#vash_valid_pair?` whether the
   given pair may be accepted or not (note: both methods operate on already
   munged keys and values).
5. If verification succeeds, the pair is passed to `#vash_munge_pair` and
   added to Vash container.

In any of these points, if the validation fails, an exception is raised. The
`Vash` by default raises `Puppet::Util::PTomulik::Vash::InvalidKeyError`,
`Puppet::Util::PTomulik::Vash::InvalidValueError` or
`Puppet::Util::PTomulik::Vash::InvalidPairError`. All of them are subclasses of
`::ArgumentError`.

Custom Vashes may be created by overwriting any of the above-mentioned methods
in your class (or any of the other methods not mentioned yet). It's also good
to prepare some specs/tests for your customized class (see [Testing](#testing)).

We'll start with simple customized Vash in [Example
1](#example-1-defining-valid-keys-and-values) and will proceed with extending
it in subsequent examples.

#### Example 1: Defining valid keys and values

Let's prepare simple container with integer variables:

```ruby
require 'puppet/util/ptomulik/vash/contained'
class Variables
  include Puppet::Util::PTomulik::Vash::Contained
  # accept only valid identifiers as keys
  def vash_valid_key?(key); key.is_a?(String) and (key=~/^[a-zA-Z]\w*$/); end
  # accept only what is convertible to integer
  def vash_valid_value?(val); true if Integer(val) rescue false; end
end
```

Perform simple tests:

```ruby
vars = Variables['ten', 10, 'nine', 9]
# => {"nine"=>9,"ten"=>10}
vars[2] = 20
# InvalidKeyError: invalid key 2
vars['eight'] = 'e'
# InvalidValueError: invalid value "e" at key "eight"
vars['seven'] = '7'
# => "7"
vars
# => {"nine"=>9, "ten"=>10, "seven"=>"7"}
```
#### Example 2: Data munging

The class from [Example 1](#example-1-defining-valid-keys-and-values) has one
shortcoming - it doesn't convert values to integers. For example
`vars['seven']` is a string. If we'd like to have integers in our container, so
we had to add data munging to our class:

```ruby
class Variables
  def vash_munge_value(val); Integer(val); end
end
```

Now we have

```ruby
vars = Variables['seven','7']
# => {"seven"=>7}
```

We may also munge keys, for example convert camelCase names to under\_score:

```ruby
class Variables
  def vash_munge_key(key); key.gsub(/([a-z])([A-Z])/,'\1_\2').downcase; end
end
```

```ruby
vars = Variables['TwentyFive','25']
# => {"twenty_five"=>25}
```

#### Example 3: Defining valid pairs

Some variables may not accept certain values. To prevent Vash from accepting
such pairs, a pair validation may be used. In this example we prevent variables
endine with `_price` to accept negative values:

```ruby
class Variables
  # for keys ending with _price we accept only non-negative values
  def vash_valid_pair?(pair); (pair[0]=~/_?price$/) ? (pair[1]>=0) : true; end
end
```

```ruby
vars = Variables['lemonPrice', '-4']
# InvalidPairError: invalid (key,value) combination ("lemon_price",-4) at index 0
```

#### Example 2: Customized key and value names

```ruby
require 'puppet/util/ptomulik/vash/contained'
class Variables
  include Puppet::Util::PTomulik::Vash::Contained
  def vash_key_name(*args); 'variable name'; end
  def vash_value_name(*args); 'variable value'; end
  def vash_pair_name(*args); ''; end
end
```

This way you easily customize the exception messages generated by Vash, for example

#### Example 2: Customized key and value names

## Reference

## Testing

## Development

The project is held at github:

* [https://github.com/ptomulik/puppet-vash](https://github.com/ptomulik/puppet-vash)

Issue reports, patches, pull requests are welcome!
