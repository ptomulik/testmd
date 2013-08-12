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

Puppet utilities to interact with package repositories. Simplifies tasks such as obtainning list of packages available for installation, their versions, installation candidates and so on. This plugin may be handy, if you need to implement facts describing packages available to agents' via their package repositories.                                                                   

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

##Setup

###What repoutil affects

  * it executes CLI commands necessary to query information from package
    repositories. The exact list of commands being executed depend on agent's OS
    and module's usage. Generally the following tools are used by `repoutil`'s: 

    - on Debian: `apt-cache show|policy`, `aptitude show`, 
    - on FreeBSD, OpenBSD, NetBSD:  `make -C /path/to/ports search`,

###Setup Requirements 

You may need to enable **pluginsync** in your *puppet.conf*.

###Beginning with repoutil

Let's start with creating an apt utility:

    require 'puppet/util/repoutil'
    repo = Puppet::Util.repoutil(:apt)
 
Our `repo` provides methods for interaction with `apt` repository. The `:apt`
utility is **suitable** for use on Debian or Ubuntu. For other systems we
should choose other facility. The most universal way is to load default utility
for current system:

    repo = Puppet::Util.defaultrepoutil

Once we have obtained `repo`, we may list all packages with names starting with
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
      .
      .
      .
    }

To retrieve full records from package database (including version, description,
and other information) you may use:

    repo.package_records_with_prefix('apache')

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

Of course, we may use exact package names, thus obtaining information for one
particular package. For example, in order to obtain available package versions
we call:

    repo.package_versions('apache2')

This method returns an array, such as `["2.4.6-2", "2.2.22-13"]` or `nil` (if
there is no database entry for the package).

To see the installation candidate for package (what version of the package
would be installed if we requested package installation/update), we do:

    repo.package_candidate('apache2')

This returns a string (e.g. `"2.4.6-2"`) or `nil` (if there is no such package
in repository).


To retrieve full records for available package versions we type:

    repo.package_records('apache2')

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

  - `Puppet::Util::RepoUtil` - represents single utility,
  - `Puppet::Util::RepoUtils` - represents the "registry" of available utilities,

and few short-hand methods.

The `Puppet::Util::RepoUtil` class abstracts CLI commands used to access
repository caches/databases. The user is actually given subclasses of
`Puppet::Util::RepoUtil`, each one corresponding to an appropriate [package
provider](http://docs.puppetlabs.com/references/latest/type.html#package) from
puppet core (note, not all puppet providers are covered here). For example,
there is `Puppet::Util::RepoUtils::Apt` which corresponds to `:apt` package
provider.

### Methods within `Puppet::Util`

  * `newrepoutil(name, options = {}, &block)` - shorthand to
    `Puppet::Util::RepoUtils.newrepoutil`
  * `repoutil(name)` - shorthand to `Puppet::Util::RepoUtils.repoutil`
  * `repoutils()` - shorthand to `Puppet::Util::RepoUtils.repoutils`
  * `suitablerepoutils()` - shorthand to
    `Puppet::Util::RepoUtils.suitablerepoutils`
  * `defaultrepoutil()` - shorthand to
    `Puppet::Util::RepoUtils.defaultrepoutil`



### Methods within `Puppet::Util::RepoUtils`

  * `newrepoutil(name, options = {}, &block)` - define new repo utility. This is
    intended for developers/contributors and may be used to add new providers to
    `repoutil`. See [adding new utility](#adding-new-utility-provider).
  * `unrepoutil(name)` - **TODO**: write documentation
  * `repoutil(name)` - **TODO**: write documentation
  * `repoutils()` - **TODO**: write documentation
  * `suitablerepoutils()` - **TODO**: write documentation
  * `defaultrepoutil()` - **TODO**: write documentation
  * `loadall()` - **TODO**: write documentation
  * `repoutilloader()` - **TODO**: write documentation
  * `package_records(packages)` - **TODO**: write documentation
  * `package_versions(packages)` - **TODO**: write documentation
  * `package_candidates(packages)` - **TODO**: write documentation

### Methods within `Puppet::Util::RepoUtil`

  * `package_name_regexp` - **TODO**: write documentation
  * `package_prefix_regexp` - **TODO**: write documentation
  * `validate_package_name(package)` - **TODO**: write documentation
  * `validate_package_prefix(prefix)` - **TODO**: write documentation
  * `package_name_to_pattern` - **TODO**: write documentation
  * `package_prefix_to_pattern` - **TODO**: write documentation
  * `candidates_cache` - **TODO**: write documentation
  * `records_cache` - **TODO**: write documentation
  * `clear_candidates_cache` - **TODO**: write documentation
  * `clear_records_cache` - **TODO**: write documentation
  * `retrieve_candidates(pattern)` - **TODO**: write documentation
  * `retrieve_records(pattern)` - **TODO**: write documentation
  * `package_records(package)` - **TODO**: write documentation
  * `package_versions(package)` - **TODO**: write documentation
  * `package_candidate(package)` - **TODO**: write documentation
  * `packages_with_prefix(prefix)` - **TODO**: write documentation
  * `package_versions_with_prefix(prefix)` - **TODO**: write documentation
  * `package_candidates_with_prefix(prefix)` - **TODO**: write documentation
  * `package_records_with_prefix(prefix)` - **TODO**: write documentation

##Reference

**TODO**: write reference 

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

The new utility may be defined with `Puppet::Util.newrepoutil` method. 

  - `Puppet::Util.newrepoutil(name, options = {}, &block)`

When defining new utility, one should provide following methods in the `block`:

  - `self.package_name_regexp`
  - `self.package_prefix_regexp`
  - `self.package_name_to_pattern`
  - `self.package_prefix_to_pattern`
  - `self.retrieve_candidates`
  - `self.retrieve_records`

To configure suitability, defaults etc., use [same methods as in normal
providers](http://docs.puppetlabs.com/guides/provider_development.html).
Here is complete template for a repoutil `foo` (based on `apt`):

    # lib/puppet/util/repoutil/foo.rb
    module Puppet::Util
      newrepoutil(:foo) do
        commands :foocmd => '/usr/bin/foo'

        def self.package_name_regexp
          /[a-z0-9][a-z0-9\.+-]+/ # adjust to CLI tools used
        end

        def self.package_prefix_regexp
          /(?:[a-z0-9][a-z0-9\.+-]*)?/ # adjust to CLI tools used
        end

        def self.escape_package_name(package)
          package.gsub(/([\.\+])/) {|c| '\\' + c} # adjust to CLI tools used
        end

        def self.escape_package_prefix(prefix)
          prefix.gsub(/([\.\+])/) {|c| '\\' + c} # adjust to CLI tool used
        end

        def self.package_name_to_pattern(package)
          "^#{escape_package_name(package)}$"
        end

        def self.package_prefix_to_pattern(prefix)
          "^#{escape_package_prefix(prefix)}"
        end

        
        def self.show_policies(pattern)
          foocmd '-q=2', '-a', 'policy', pattern
        end

        def self.show_records(pattern)
          foocmd '-q=2', '-a', 'show', pattern
        end

        def self.retrieve_candidates(pattern)
          output = show_policies(pattern)
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
