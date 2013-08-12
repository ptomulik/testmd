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

Puppet utilities to interact with package repositories. Simplifies tasks such
as obtainning list of packages available for installation, their versions,
installation candidates and so on.  This plugin may be handy, if you need to
implement some facts with information about certain packages available to
agents via their package repositories.

##Module Description

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

The supported operations currently include: 

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

You may need to enable **pluginsync**.

###Beginning with repoutil

Let's start with creating an apt utility:

    require 'puppet/util/repoutil'
    repo = Puppet::Util.repoutil(:apt)
 
Our `repo` provides methods for interaction with apt repositories (to be
exemplified below). The `:apt` utility is **suitable** for use on Debian or
Ubuntu. For other systems we should choose other facility. We may simply load
default utility for the local system:

    repo = Puppet::Util.defaultrepoutil

Once we have the `repo`, we may list all packages with names starting with
'apache':

    apaches = repo.packages_with_prefix('apache')

This would return an array of all available package names starting with
'apache' prefix, for example:

    ["apache2-utils", "apache2-mpm-prefork", "apache2-mpm-worker",
     "apache2-dbg", "apache2.2-bin", "apache2-mpm-event", "apache2-mpm-itk",
     "apache2-dev", "apachetop", "apache2-prefork-dev", "apache2-doc",
     "apache2-suexec-custom", "apache2-suexec", "apache2-data", "apache2",
     "apache2-bin", "apache2-threaded-dev", "apache2-suexec-pristine",
     "apache2.2-common"]

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
          .
          .
          .
        },
        "2.2.22-13" => {
          "Package"=>"apache2",
          "Version"=>"2.2.22-13",
          "Installed-Size"=>"29",
          .
          .
          .
        }
      },

      "apache2-mpm-prefork => {
        .
        .
        .
      },

      .
      .
      .
    }

There is one entry for each package, which in turn has one entry for each
installable version of that package.

We may also use exact package names. To obtain available package versions we
call:

    repo.package_versions('apache2')

which returns an array, for example `["2.4.6-2", "2.2.22-13"]` (if there is no
database entry for the package, `nil` is returned).

To see the installation candidate for package, we do:

    repo.package_candidate('apache2')

which returns a string (e.g. `"2.4.6-2"`) or `nil` (if there is no such package
in repo).


To retrieve full records for available package versions we type:

    repo.package_records('apache2')

This should return a has as follows:

    {
      "2.4.6-2" => 
      {
        "Package"=>"apache2",
        "Version"=>"2.4.6-2",
        "Installed-Size"=>"481",
        .
        .
        .
      },
      "2.2.22-13" => 
      {
        "Package"=>"apache2",
        "Version"=>"2.2.22-13",
        "Installed-Size"=>"29",
        .
        .
        .
      }
    }

In case there is no such package in repository, `nil` is returned.

##Usage

* Methods of `Puppet::Util::RepoUtils`

  - `newrepoutil(name, ...)`

    **TODO**: write documentation

  - `unrepoutil(name)`

    **TODO**: write documentation

  - `repoutil(name)`

    **TODO**: write documentation

  - `repoutils()`

    **TODO**: write documentation
    
  - `suitablerepoutils()`

    **TODO**: write documentation

  - `defaultrepoutil()`

    **TODO**: write documentation

  - `loadall()`

    **TODO**: write documentation

  - `repoutilloader()`

    **TODO**: write documentation

  - `package_records(packages)`

    **TODO**: write documentation

  - `package_versions(packages)`
    
    **TODO**: write documentation

  - `package_candidates(packages)`

    **TODO**: write documentation

##Reference

Here, list the classes, types, providers, facts, etc contained in your module. This section should include all of the under-the-hood workings of your module so people know what the module is touching on their system but don't need to mess with things. (We are working on automating this section!)

##Limitations

This is where you list OS compatibility, version compatibility, etc.

##Development

The project is held on github:

  https://github.com/ptomulik/puppet-repoutil

Feel free to submit bug reports, feature requests or to create pull requests.


##Release Notes/Contributors/Etc **Optional**

If you aren't using changelog, put your release notes here (though you should consider using changelog). You may also add any additional sections you feel are necessary or important to include here. Please use the `## ` header. 
