#ptomulik-repoutil

####Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup](#setup)
    * [What repoutil affects](#what-[modulename]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with repoutil](#beginning-with-repoutil)
4. [Usage](#usage)
5. [Reference](#reference)
5. [Limitations](#limitations)
6. [Development](#development)

##Overview

Puppet utilities to interact with package repositories. Simplify tasks such
as obtaining lists of packages available for installation, their versions,
installation candidates and so on. This plugin may be handy, if you need to
implement facts describing packages available to agents' via their package
repositories.

##Module Description

The module provides means to access meta-information from package repositories
such as apt or yum. The supported operations currently include: 

  * listing available packages,
  * listing available package(s) versions,
  * retrieving full package records (containing descriptions, etc.),
  * determining installation candidate (version) for a given package,

The module currently supports the following providers:

  * apt, aptitude (Debian),
  * ports (FreeBSD)

Utilities provided by `repoutil` are devoted to developers of other modules,
and are not intended to be used directly from puppet manifests. 

##Setup

###What repoutil affects

  * it executes CLI commands necessary to query information from package
    repositories. The exact list of commands being executed depend on agent's OS
    and module's usage, but generally they should be regarded as harmless. 
    
    The following CLI commands are currently used by `repoutil`'s: 

    - on Debian: `apt-cache show|policy`, `aptitude show`, 
    - on FreeBSD, OpenBSD, NetBSD:  `make -C /path/to/ports search`,

###Setup Requirements 

You may need to enable **pluginsync** in your *puppet.conf*.

###Beginning with repoutil

Let say, you're developing your custom fact or resource type and you need to
characterize some packages existing in your package repository. To use repoutil
utilities, you should first include appropriate module file:

    require 'puppet/util/repoutil'

One of the first steps you'll probably have to do is to retrieve an utility
provider (call it `repo`). For example, apt `repo` may be obtained as follows:

    repo = Puppet::Util.repoutil(:apt)
 
The `:apt` utility is **suitable** for use on Debian or Ubuntu. For other
systems we should choose other facility. The most universal way is to load
default utility for current environment: 

    repo = Puppet::Util.defaultrepoutil

Once we have obtained `repo`, we may request information from that repository.
For example, we may wish to list all packages with names starting with
'apache':

    apaches = repo.packages_with_prefix('apache')

This would return an array of all available package names starting with
'apache' prefix, for example:

    ["apache2-utils", "apache2-mpm-prefork", "apache2-mpm-worker", ... ]

There are also other methods. For example:

    apache_versions = repo.package_versions_with_prefix('apache')

would return a hash with keys being the package names (from previous example)
and arrays of available versions as values:

    { 
      "apache2"             => ["2.4.6-2", "2.2.22-13"],
      "apache2-mpm-prefork" => ["2.4.6-2", "2.2.22-13"], 
      "apache2-mpm-itk"     => ["2.4.6-2", "2.2.22-13"],
      . . .
    }

To retrieve full records from package database (including version, description,
and other information) you may use:

    apache_records = repo.package_records_with_prefix('apache')

This would return a hash such as the following:

    { 
      "apache2" => {
        "2.4.6-2" => {
          "Package"=>"apache2",
          "Version"=>"2.4.6-2",
          "Installed-Size"=>"481",
          . . .
        },
        "2.2.22-13" => {
          "Package"=>"apache2",
          "Version"=>"2.2.22-13",
          "Installed-Size"=>"29",
          . . .
        }
      },

      "apache2-mpm-prefork => {
        . . .
      },

      .
      .
      .
    }

Note, that the returned hash has one item per package, and each such item
consists of several sub-items - one for each installable version of a package.

Of course, we may operate on exact package names, that is request information
for one particular package. For example, in order to obtain available package
versions we call:

    apache2_versions = repo.package_versions('apache2')

This method returns an array, such as `["2.4.6-2", "2.2.22-13"]` or `nil` (if
there is no database entry for the package).

To see the installation candidate for package (what version of the package
would be installed if we requested package installation/upgrade), we do:

    apache2_candidates = repo.package_candidate('apache2')

This returns a string (e.g. `"2.4.6-2"`) or `nil` (if there is no such package
in repository).


To retrieve full records for available package versions we type:

    apache2_records = repo.package_records('apache2')

This should return a hash as follows:

    {
      "2.4.6-2" => 
      {
        "Package"=>"apache2",
        "Version"=>"2.4.6-2",
        "Installed-Size"=>"481",
        . . .
      },
      "2.2.22-13" => 
      {
        "Package"=>"apache2",
        "Version"=>"2.2.22-13",
        "Installed-Size"=>"29",
        . . .
      }
    }

In case there is no such package in repository, `nil` is returned.

##Usage

The module consists of two ruby classes: 

  - [`Puppet::Util::RepoUtil`](#methods-within-puppetutilrepoutil-class) -
    represents single utility,
  - [`Puppet::Util::RepoUtils`](#methods-within-puppetutilrepoutils-class) -
    represents the "registry" of available utilities,

and few [shorthand module-level methods](#methods-within-puppetutil-module).

The [`Puppet::Util::RepoUtil`](#methods-within-puppetutilrepoutil-class) class
abstracts CLI commands used to access repository cache/database and is
inherited by several sub-classes implementing particular types of repositories
(providers). The user actually operates on providers, that is on subclasses of
`Puppet::Util::RepoUtil`. Each *repoutil provider* shall correspond to an
appropriate [package provider](http://docs.puppetlabs.com/references/latest/type.html#package)
from puppet core (note, not all puppet providers are covered here). For
example, there is `Puppet::Util::RepoUtils::Apt` class (`:apt` repoutil) which
corresponds to `:apt` package provider. To retrieve appropriate repoutil, you
should use one of the methods from `Puppet::Util::RepoUtils` described below.
Methods within `Puppet::Util::RepoUtil` (and descendants) may be used to
operate on a single repository.

[Methods within `Puppet::Util::RepoUtils`](#methods-within-puppetutilrepoutils-class)
are twofold. Some of them are provided to 
[manage providers](#provider-management). These include
[`newrepoutil`](#newrepoutilname-options---block-1) (to implement new
providers), [`unrepoutil`](#unrepoutilname) (to unregister particular
provider), [`repoutils`](#repoutils-1) (to retrieve all available providers),
[`suitablerepoutils`](#suitablerepoutils-1) (to retrieve all the providers
suitable for the current environment), [`defaultrepoutil`](#defaultrepoutil-1)
to get provider default to current environment and
[`repoutil`](#repoutilname-1) (to retrieve particular provider). Other methods
within `Puppet::Util::RepoUtils` may be used to perform 
[collective operations](#collective-operations-on-repositories)
on repositories. For example, [`package_candidates`](#package_candidatespackages)
may be used to retrieve lists of package candidates known to the suitable
package repositories (this yields a hash of the form `{:apt => {...}, :aptitude
=> {...}, ...}`).

##Reference

**TODO**: write reference 

### Methods within `Puppet::Util` module

##### newrepoutil(name, options = {}, &block)

Shorthand to [`Puppet::Util::RepoUtils.newrepoutil`](#newrepoutilname-options--block-1).

##### repoutil(name) 

Shorthand to [`Puppet::Util::RepoUtils.repoutil`](#repoutilname-1).

##### repoutils()

Shorthand to [`Puppet::Util::RepoUtils.repoutils`](#repoutils-1).

##### suitablerepoutils()

Shorthand to [`Puppet::Util::RepoUtils.suitablerepoutils`](#suitablerepoutils-1).

##### defaultrepoutil()

Shorthand to [`Puppet::Util::RepoUtils.defaultrepoutil`](#defaultrepoutil-1). 


### Methods within `Puppet::Util::RepoUtils` class

#### Provider management

##### newrepoutil(name, options = {}, &block) 

Define new repo provider. This is intended for developers/contributors and may
be used to add support for new repository types. For details about extending 
repoutil module see [adding new utility](#adding-new-utility-provider).

##### unrepoutil(name)

Unload/unregister a repoutil provider created with [`newrepoutil(name)`](#newrepoutilname-1).

##### repoutil(name)

Retrieve repoutil provider identified by `name`. The provider must be first
created with [`newrepoutil(name, ...)`](#newrepoutilname-options--block-1).

*Example:*

    repo = Puppet::Util::RepoUtils.repoutil(:apt)
    candidate = repo.package_candidate('apache2')

##### repoutils()

Retrieve all existing repoutil providers (includes also those not suitable for
a given environment).

*Example:*

    repos = Puppet::Util::RepoUtils.repoutils
    apache2_candidates = {}
    repos.each do |repo|
      if repo.suitable?
        apache2_candidates[repo] = repo.package_candidate('apache2')
      end
    end

##### suitablerepoutils()

Retrieve all repoutil providers suitable for the current environment.

*Example:*

    repos = Puppet::Util::RepoUtils.suitablerepoutils
    apache2_candidates = {}
    repos.each do |repo|
      apache2_candidates[repo] = repo.package_candidate('apache2')
    end

##### defaultrepoutil()

Retrieve repoutil provider that is default to current environment.

*Example:*

    repo = Puppet::Util::RepoUtils.defaultrepoutil
    candidates = repo.package_candidate('apache2')

##### loadall()

For internal use.

##### repoutilloader()

For internal use.

#### Collective operations on repositories

##### package\_records(packages, utils = suitablerepoutils)

Return package records obtained from multiple sources. This function performs
query on multiple repositories at once. This approach may be used to gather
package information from multiple repository types available to the local
environment.

Arguments:

  * `packages` - package name (exact) or an array of (exact) package names,
  * `utils` - list of repoutil providers to be queried, defaults to all
     suitable providers.

*Example*:

    records = Puppet::Util::RepoUtils.package_records('apache2')

Would return a hash of the form:

    { :apt  => { 
        'apache2' => {  
          '2.2.13-2' => { ... },
          '2.4.6-2'  => { ... } 
        }
      },
      :aptitude => { 
        'apache2' => {
          '2.2.13-2' => { ... } 
          '2.4.6-2'  => { ... } 
        } 
      },
       ...
     }

where `:apt`, `:aptitude`, ..., are keys corresponding to repoutil providers
listed in `utils`. Hashes obtained from expression `records[utils[n].name]`
have same structure as the these returned by
[`Puppet::Util::RepoUtil.package_recordspackage`](#package_recordsname).

##### package\_versions(packages, utils = suitablerepoutils)

**TODO**: write documentation

##### package\_candidates(packages, utils = suitablerepoutils)

**TODO**: write documentation

### Methods within `Puppet::Util::RepoUtil` class

##### package\_name\_regexp

**TODO**: write documentation

##### package\_prefix\_regexp

**TODO**: write documentation

##### validate\_package\_name(package)

**TODO**: write documentation

##### validate\_package\_prefix(prefix)

**TODO**: write documentation

##### package\_name\_to\_pattern

**TODO**: write documentation

##### package\_prefix\_to\_pattern

**TODO**: write documentation

##### candidates\_cache

**TODO**: write documentation

##### records\_cache

**TODO**: write documentation

##### clear\_candidates\_cache

**TODO**: write documentation

##### clear\_records\_cache

**TODO**: write documentation

##### retrieve\_candidates(pattern)

**TODO**: write documentation

##### retrieve\_records(pattern)

**TODO**: write documentation

##### package\_records(package)

**TODO**: write documentation

##### package\_versions(package)

**TODO**: write documentation

##### package\_candidate(package)

**TODO**: write documentation

##### packages\_with\_prefix(prefix)

**TODO**: write documentation

##### package\_versions\_with\_prefix(prefix)

**TODO**: write documentation

##### package\_candidates\_with\_prefix(prefix)

**TODO**: write documentation

##### package\_records\_with\_prefix(prefix)

**TODO**: write documentation


##Limitations

  * Currently supports only Debian/Ubuntu *apt*, *aptitude* and FreeBSD *ports*
  * Some tests are missing.

**TODO**: enumerate other limitations 

##Development

The project is held on github:

  https://github.com/ptomulik/puppet-repoutil

Feel free to submit bug reports, feature requests or to create pull requests.

### Extending repoutil

#### Adding new utility (provider)

The new utility may be defined by using `Puppet::Util.newrepoutil` method. 

  - `Puppet::Util.newrepoutil(name, options = {}, &block)`

If you're extending `repoutil` module, the source code of the new provider (say
`foo`) should go to file 

  - `lib/puppet/util/repoutil/foo.rb`

You may also consider adding `:foo` entry to `utils` array in 

  - `spec/unit/puppet/util/repoutil_spec.rb`

to enable common unit tests for your provider. Specific tests should be
implemented in

  - `spec/unit/puppet/util/repoutil/foo_spec.rb`

When defining new utility, one should define following methods in the block
provided to [`newrepoutil`](#newrepoutilname-options--block-1):

  - `self.package_name_regexp`
  - `self.package_prefix_regexp`
  - `self.package_name_to_pattern(package)`
  - `self.package_prefix_to_pattern(prefix)`
  - `self.retrieve_candidates(pattern)`
  - `self.retrieve_records(pattern)`

To configure suitability, confines, defaults etc., use [same methods as in
normal providers](http://docs.puppetlabs.com/guides/provider_development.html).

Example template for a repoutil `foo` is presented below:

    # lib/puppet/util/repoutil/foo.rb
    module Puppet::Util
      newrepoutil(:foo) do
        commands :foocmd => '/usr/bin/foo'

        def self.package_name_regexp
          # this is tool-specific, check if this pattern fits your needs
          /[a-z0-9][a-z0-9\.+-]+/ 
        end

        def self.package_prefix_regexp
          # this is tool-specific, check if this pattern fits your needs
          /(?:[a-z0-9][a-z0-9\.+-]*)?/ 
        end

        def self.escape_package_name(package)
          # this is tool-specific, check if this pattern fits your needs
          package.gsub(/([\.\+])/) {|c| '\\' + c}
        end

        def self.escape_package_prefix(prefix)
          # this is tool-specific, check if this pattern fits your needs
          prefix.gsub(/([\.\+])/) {|c| '\\' + c}
        end

        def self.package_name_to_pattern(package)
          # this is tool-specific, check if this conversion fits your needs
          "^#{escape_package_name(package)}$"
        end

        def self.package_prefix_to_pattern(prefix)
          # this is tool-specific, check if this conversion fits your needs
          "^#{escape_package_prefix(prefix)}"
        end

        def self.show_candidates(pattern)
          # adjust to the actual syntax of your CLI command
          foocmd 'candidates',  pattern 
        end

        def self.show_records(pattern)
          # adjust to the actual syntax of your CLI command
          foocmd 'records', pattern
        end

        def self.retrieve_candidates(pattern)
          output = show_candidates(pattern).chomp
          # now, extract candidates from output, update candidates_cache and
          # return the extracted candidates
        end

        def self.retrieve_records(pattern)
          output = show_records(pattern).chomp
          # now, extract records from output, update records_cache and
          # return the extracted records
        end
      end
    end
