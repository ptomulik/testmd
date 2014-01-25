#ptomulik-macro

[![Build Status](https://travis-ci.org/ptomulik/puppet-macro.png?branch=master)](https://travis-ci.org/ptomulik/puppet-macro)
[![Coverage Status](https://coveralls.io/repos/ptomulik/puppet-macro/badge.png)](https://coveralls.io/r/ptomulik/puppet-macro)
[![Code Climate](https://codeclimate.com/github/ptomulik/puppet-macro.png)](https://codeclimate.com/github/ptomulik/puppet-macro)

####Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Usage](#usage)
   * [Example 1: Defining macro in ruby code](#example-1-defining-macro-in-ruby-code)
   * [Example 2: Invoking macro in puppet manifest](#example-2-invoking-macro-in-puppet-manifest)
   * [Example 3: Macro with parameters](#example-3-macro-with-parameters)
   * [Example 4: Variable number of parameters](#example-4-variable-number-of-parameters)
   * [Example 5: Default parameters](#example-5-default-parameters)
   * [Example 6: Invoking macro from macro](#example-6-invoking-macro-from-macro)
   * [Example 7: Using facts](#example-7-using-facts)
   * [Example 8: Building dependencies between parameters](#example-8-building-dependencies-between-parameters)
4. [Reference](#reference)
5. [Limitations](#limitations)

##<a id="overview"></a>Overview

Puppet parser macros.

##<a id="module-description"></a>Module Description

With this functionality module developers may define named macros and evaluate them in manifests. This works similarly to parser [functions](http://docs.puppetlabs.com/guides/custom_functions.html) but implementing a macro is a little bit easier. Alwo, macros may use "hierarchical" names in `foo::bar::geez` form, so a module developer may easily establish one-to-one correspondence between macro names and class/define parameters. Finally, the macros may be defined in a hierarchy of ruby files instead of a list of files lying in a single directory.

##<a id="usage"></a>Usage

###<a id="example-1-defining-macro-in-ruby-code"></a>Example 1: Defining macro in ruby code

To define a macro named **foo::bar** in a module **mymodule** write a file
named *mymodule/lib/puppet/parser/macros/foo/bar.rb*:

```ruby
# mymodule/lib/puppet/parser/macros/foo/bar.rb
Puppet::Parser.newmacro 'foo::bar' do
  setcode do
    'macro foo::bar'
  end
end
```

The above macro simply returns the `'macro foo::bar'` string.

###<a id="example-2-invoking-macro-in-puppet-manifest"></a>Example 2: invoking macro in puppet-manifest

Nothing simpler that:

```puppet
$foo_bar = determine('foo::bar')
```

If you don't need the value returned by macro, then the you may invoke macro as a statement:

```puppet
invoke('foo::bar')
```

###<a id="example-3-macro-with-parameters"></a>Example 3: macro with parameters

Let's define macro `sum` which adds two integers:

```ruby
# mymodule/lib/puppet/parser/macros/sum.rb
Puppet::Parser.newmacro 'foo::bar' do
  setcode do |x,y|
    Integer(x) + Integer(y)
  end
end
```

Now you may use it with:

```puppet
$a = determine('sum', 1, 2)
```

###<a id="example-4-variable-number-of-parameters"></a>Example 4: variable number of parameters

Let's redefine our `sum` from [Example 3](#example-3-macro-with-parameters) to accept arbitrary number of parameters:

```ruby
# mymodule/lib/puppet/parser/macros/sum.rb
Puppet::Parser.newmacro 'foo::bar' do
  setcode do |*args|
    args.reduce(0,:+)
  end
end
```

Now you may use it with:

```puppet
$zero = determine('sum')
$one = determine('sum',1)
$three = determine('sum',1,2)
```

###<a id="example-5-default-parameters"></a>Example 5: Default parameters

For default parameters one may have to slightly change the way of defining macro's code. This is specific to **ruby 1.8**, so if you're sure your module will be used only with **1.9+** just don't care. If, however, you plan to be compatible with ruby **1.8**, define a macro in the following way:

```ruby
# mymodule/lib/puppet/parser/macros/puppet/config/content.rb
Puppet::Parser.newmacro 'puppet::config::content' do
  def call(file='/etc/puppet/puppet.conf')
    File.read(file)
  end
  setcode
end
```

If you don't care about compatibility with ruby **1.8**, define a macro in the usual way:
```ruby
# mymodule/lib/puppet/parser/macros/puppet/config/content.rb
Puppet::Parser.newmacro 'puppet::config::content' do
  setcode do |file='/etc/puppet/puppet.conf'|
    File.read(file)
  end
end
```

Now you may use it with:

```puppet
$content = determine('apache::config::content')
```

or
```puppet
$content = determine('apache::config::content','/usr/local/etc/puppet/puppet.conf')
```

###<a id="example-6-invoking-macro-from-macro"></a>Example 6: Invoking macro from macro

For this we have special convenience methods `determine` and `invoke`, for example:

```ruby
# mymodule/lib/puppet/parser/macros/foo.rb
Puppet::Parser.newmacro 'foo' do
  setcode do
    determine('bar')
  end
end
```

###<a id="example-7-using-facts"></a>Example 7: Using facts

To determine default location of apache configs, for example:

```ruby
# mymodule/lib/puppet/parser/macros/apache/conf_dir.rb
Puppet::Parser.newmacro 'apache::conf_dir' do
  setcode do
    case os = fact(:osfamily)
    when /FreeBSD/; '/usr/local/etc/apache22'
    when /Debian/; '/usr/etc/apache2'
    else
      raise Puppet::Error, "unsupported osfamily #{os}"
    end
  end
end
```

###<a id="example-8-building-dependencies-between-parameters"></a>Example 8: Building dependencies between parameters

Macros may be used to define types or classes whose parameters are interdependent. In other words, if one parameter is altered by user, others should be adjusted automatically, unless user speciy them as well. For example:

```ruby
# mymodule/lib/puppet/parser/macros/foo/a.rb
Puppet::Parser.newmacro 'foo::a' do
  setcode do
    'default a'
  end
end
```

```ruby
# mymodule/lib/puppet/parser/macros/foo/b.rb
Puppet::Parser.newmacro 'foo::b' do
  setcode do |a|
    a = determine('foo::a') if not a or a.empty? of a.equal? :undef
    "default b for a=#{a.inspect}"
  end
end
```

```puppet
define foo( $a = determine('foo::a'),
            $b = determine('foo::b', $a) )
{
  notify{$title: "${title}: a=\'${a}\', b=\'${b}\'"}
}
foo {defaults: }
foo {custom_a: a => 'custom a' }
foo {custom_b: b => 'custom b' }
foo {custom_a_and_b: a => 'custom a', b => 'custom b' }
```

##<a id="reference"></a>Reference

Here, list the classes, types, providers, facts, etc contained in your module. This section should include all of the under-the-hood workings of your module so people know what the module is touching on their system but don't need to mess with things. (We are working on automating this section!)

##Limitations

* Currently there is no possiblity to define macro in puppet manifests, that is we only can define macro using ruby and use it in ruby or puppet. I believe this functionality may implemented as an additional parser function (call it `macro`) and it should work with the help of puppet lambdas, which are available in future parser.
