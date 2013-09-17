# ptomulik-vash

[![Build Status](https://travis-ci.org/ptomulik/puppet-vash.png?branch=master)](https://travis-ci.org/ptomulik/puppet-vash)

#### Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Beginning with vash](#beginning-with-vash)
4. [Usage](#usage)
5. [Reference](#reference)
6. [Testing](#testing)
7. [Limitations](#limitations)
8. [Development](#development)

## Overview

*Vash* (a validating hash) provides mixins to add
[Hash](http://www.ruby-doc.org/core/Hash.html) methods to user class enabling
simple validation and munging of data entering that hash.

# Module Description

Vash provides mixins that add Hash interface to classes. The mixins allow you
to enable simple data validation and munging, such that you may define
restrictions on keys, values and pairs entering your hash and coalesce them
at input.

## Beginning with vash

There are two ways to add *Vash* functionality to your class. The first one is
to use `Vash::Contained` mixin, as follows

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
way `Hash` does, accepting any input data and not modifying it.

Simple validation may be added by defining `vash_valid_key?`,
`vash_valid_value?` and `vash_valid_pair?` methods (note, all methods that are
specific to Vash, have `vash_` prefix):

```ruby
require 'puppet/util/ptomulik/vash/contained'
class MyVash
  include Puppet::Util::PTomulik::Vash::Contained
  # accept only integers as keys
  def vash_valid_key?(key)
    true if Integer(key) rescue false
  end
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

Custom Vashes may be created by including
`Puppet::Util::PTomulik::Vash::Contained` or
`Puppet::Util::PTomulik::Vash::Inherited` mixin to your class and then
overwriting some of its methods. It's also good to prepare some specs/tests for
your customized class (see [Testing](#testing)).  We'll start with simple
customized Vash in
[Example 3.1](#example-31-defining-restrictions-for-keys-and-values)
and will continue extending it in subsequent examples.

#### Example 3.1: Defining restrictions for keys and values

Let's prepare simple container with integer variables:

```ruby
require 'puppet/util/ptomulik/vash/contained'
class Variables
  include Puppet::Util::PTomulik::Vash::Contained
  # accept only valid identifiers as keys
  def vash_valid_key?(key)
    key.is_a?(String) and (key=~/^[a-zA-Z]\w*$/)
  end
  # accept only what is convertible to integer
  def vash_valid_value?(val)
    true if Integer(val) rescue false
  end
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
#### Example 3.2: Munging keys and values

The class from [Example 3.1](#example-31-defining-restrictions-for-keys-and-values)
has one drawback - it doesn't convert values to integers. For example
`vars['seven']` is `"7"` (a string). Value munging may be added to `Variables`
in order to convert data provided by user to integers.

```ruby
class Variables
  def vash_munge_value(val)
    Integer(val)
  end
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
  def vash_munge_key(key)
    key.gsub(/([a-z])([A-Z])/,'\1_\2').downcase
  end
end
```

```ruby
vars = Variables['TwentyFive','25']
# => {"twenty_five"=>25}
```

#### Example 3.3: Defining restrictions for pairs

Some variables may not accept certain values. To prevent Vash from accepting
such pairs, a pair validation may be used. In this example we prevent variables
ending with `_price` from accepting negative values:

```ruby
class Variables
  # for keys ending with _price we accept only non-negative values
  def vash_valid_pair?(pair)
    (pair[0]=~/price$/) ? (pair[1]>=0) : true
  end
end
```

```ruby
vars = Variables['lemonPrice', '-4']
# InvalidPairError: invalid (key,value) combination ("lemon_price",-4) at index 0
```

#### Example 3.4: Munging pairs

We may also munge pairs entering our `Variables` container. In this example
we'll append variable value to its name.


```ruby
class Variables
  def vash_munge_pair(pair)
    [pair[0] + pair[1].to_s, pair[1]]
  end
end
```

```ruby
vars = Variables['myVar', 1]
# => {"my_var1"=>1}
vars['my_var'] = 2
# => 2
vars
# => {"my_var2"=>2, "my_var1"=>1}
```

#### Example 3.5: Customizing error messages

Default error messages may be misleading in certain applications. To circumvent
this, we may override `vash_key_name`, `vash_value_name` and `vash_pair_name`,
for example:

```ruby
class Variables
  def vash_key_name(*args); 'variable name'; end
  def vash_value_name(*args); 'variable value'; end
  def vash_pair_name(*args); 'value for variable'; end
end
```

```ruby
vars = Variables[:xxx, 1]
# InvalidKeyError: invalid variable name :xxx at index 0
vars = Variables['var', 'xxx']
# InvalidValueError: invalid variable value "xxx" at index 1
vars = Variables['lemonPrice', '-4']
# InvalidPairError: invalid value for variable ("lemon_price",-4) at index 0
```

The last message is still not well-formed. To correct this, we may reimplement
`#vash_pair_exception`.

```ruby
class Variables
  # note: args[0] optionally contains index of a failing pair
  def vash_pair_exception(pair, *args)
    msg  = "invalid value #{pair[1].inspect} for variable #{pair[0].inspect}"
    msg += " at index #{args[0]}" unless args[0].nil?
    [Puppet::Util::PTomulik::Vash::InvalidPairError, msg]
  end
end
```

```ruby
vars = Variables['lemonPrice', -1]
# InvalidPairError: invalid value -1 for variable lemon_price at index 0
```

## Reference

When new data enters `Vash`, the workflow is following:

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


## Testing

To run existing unit tests simply type

```bash
bundle exec rake spec
```

Note, that you may need to install necessary gems to run tests:

```bash
bundle install --path vendor/bundle
```

### Shared examples overview

The module provides quite extensive set of rspec shared examples for
developers. The tests are designed such that they compare behaviour of a
subject class with a behaviour of an already-tested class such as standard
`Hash`.  Reusable *shared\_examples* are provided for developers who want to
implement cusom `Vash` classes (these examples may be actually used to test any
class that is intended to behave as Hash, Vash::Validator or complete Vash). 

The shared examples may be found in the following files:

* *spec/unit/puppet/shared_behaviours/ptomulik/vash/hash.rb*
* *spec/unit/puppet/shared_behaviours/ptomulik/vash/validator.rb*
* *spec/unit/puppet/shared_behaviours/ptomulik/vash.rb*

#### Example 6.1

Say, we want to ensure, that our new class:

```ruby
class MyHash < Hash
end
```

has all the functionality of standard `Hash`. We may use `Vash::Hash` 
*shared\_examples* to verify, that our class has the expected behaviour:

```ruby
# spec/unit/my_hash_spec.rb
require 'spec_helper'
require 'unit/puppet/shared_behaviours/ptomulik/vash/hash'

class MyHash < Hash; end

describe MyHash do
  it_behaves_like 'Vash::Hash', {
    :sample_items   => [ [:a,:A,], ['b','B'] ],
    :hash_arguments => [ { :a=>:X, :d=>:D } ],
    :missing_key    => :c,
    :missing_value  => :C
  }
end

```

The `:sample_items` array is used to initialize hash during the tests and also
to generate input arguments to some hash functions (keys/values from
`sample_items` may be used as `existing_key` and `existing_value`). The
`:hash_arguments` is an array of hashes used to test methods accepting hash as
an argument (e.g. `merge!`). The `:missing_key` and `:missing_value` are sample
key and value that are correct (should pass key/value and pair validation) but
is not present in `:sample_items`.

The above snippet shall generate about 700 test cases. Because `MyHash` has all
the functionality of (its base class) `Hash`, we expect all tests to pass. 

### Shared examples reference

#### `Vash::Hash` shared examples

*Synopsis*:

```ruby
it_behaves_like 'Vash::Hash', params
```

where `params` is a Hash with parameters for test driver.

*Example usage*:

```ruby
require 'unit/puppet/shared_behaviours/ptomulik/vash/hash'
# ...
# MyHash is the class under test
describe MyHash do
  it_behaves_like 'Vash::Hash', {
    :sample_items   => [ [:a,:A,], ['b','B'] ],
    :hash_arguments => [ { :a=>:X, :d=>:D } ],
    :missing_key    => :c,
    :missing_value  => :C
  }
end
```

*Parameters*:

* `model` (optional) - an object which models expected Hash behaviour, the
  `model` object is not used by its own, but `model.class` is used by the
  shared examples,
* `model_class` (optional) - a class which models expected Hash behaviour,
   by default `Puppet::SharedBehaviours::PTomulik::Vash::Hash` is used,
   which is direct subclass of standard `Hash`,
* `methods` (optional) - a hash of procs/lambdas which override appropriate
  methods in the behaviour class. This may be used to slightly modify model
  behaviour used by shared examples, for example:
  ```ruby
  # slightly modified hash ...
  class MyHash < Hash
    def default; nil; end
    def default=(arg)
      raise RuntimeError, "can't change default for MyHash"
    end
  end
  describe MyHash do
    it_behaves_like 'Vash::Hash', {
      # ... other params ...
      :methods => {
        :default  => lambda { nil } # our #default method always returns nil
        :default= => lambda { raise RuntimeError, "can't change default for MyHash" }
      }
    }
  end
  ```

#### `Vash::Validator` shared examples
#### `Vash` shared examples

## Development

The project is held at github:

* [https://github.com/ptomulik/puppet-vash](https://github.com/ptomulik/puppet-vash)

Issue reports, patches, pull requests are welcome!
