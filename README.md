#ptomulik-repoutil

####Table of Contents

1. [Overview](#overview)
2. [Module Description](#module-description)
3. [Setup - The basics of getting started with [Modulename]](#setup)
    * [What [Modulename] affects](#what-[modulename]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with [Modulename]](#beginning-with-[Modulename])
4. [Usage](#usage)
5. [Reference](#reference)
5. [Limitations](#limitations)
6. [Development](#development)

##Overview

Puppet utility plugin to interact with systems' package repositories. This
module simplifies such tasks as obtainning list of packages available for
installation, their versions, installation candidates and so on.
This package may be for you, if you need to implement some facts containing
information about availablility of a given package in system repositories, or
their versions.

##Module Description

The module consists of two ruby classes: 

  - `Puppet::Util::RepoUtil` - represents single utility,
  - `Puppet::Util::RepoUtils` - represents the "registry" of available utilities,

and few short-hand methods.

The `Puppet::Util::RepoUtil` class abstracts CLI commands used to access
repository caches/databases. The user is actually given subclasses of
`Puppet::Util::RepoUtil`, each one corresponding to an appropriate [`package`
provider](http://docs.puppetlabs.com/references/latest/type.html#package) from
puppet core (note, not all puppet providers are covered here). For example,
there is `Puppet::Util::RepoUtils::Apt` which corresponds to `:apt` package
provider.

The supported operations currently include: 

  * retrieving list of package names (with given prefix e.g.),
  * listing available package(s) versions,
  * guessing what's the installation candidate version for a given package,

##Setup

###What [Modulename] affects

* Executes commands responsible for querying information about package
  repositories. What command may actually be executed depends on agent's OS and
  module's usage. These tools may be in general executed by `repoutil`'s
  methods:
  - on Debian: `apt-cache show|policy`, `aptitude show`, 
  - on FreeBSD, OpenBSD, NetBSD:  `make -C /path/to/ports search`,

###Setup Requirements 

You may need to enable **pluginsync**.

###Beginning with [Modulename]

Let's start with obtaining a RepoUtil for the apt provider:

    require 'puppet/util/repoutil'
    repo = Puppet::Util.repoutil(:apt)

Now, we may, for example, list all packages with names starting with 'apache':

    apaches = repo.packages_with_prefix('apache')

This would return an array of all available package names starting with
'apache' prefix, for example:

    ["apache2-utils", "apache2-mpm-prefork", "apache2-mpm-worker",
     "apache2-dbg", "apache2.2-bin", "apache2-mpm-event", "apache2-mpm-itk",
     "apache2-dev", "apachetop", "apache2-prefork-dev", "apache2-doc",
     "apache2-suexec-custom", "apache2-suexec", "apache2-data", "apache2",
     "apache2-bin", "apache2-threaded-dev", "apache2-suexec-pristine",
     "apache2.2-common"]

There are other, perhaps more useful methods. For example:

    apache_versions = repo.package_versions_with_prefix('apache')

would return a hash with keys being the package names from previous example and
arrays of available versions as values:

    { "apache2"             => ["2.4.6-2", "2.2.22-13"],
      "apache2-mpm-prefork" => ["2.4.6-2", "2.2.22-13"], 
      "apache2-mpm-itk"     => ["2.4.6-2", "2.2.22-13"], ... }

To retrieve full records from repo database (including version, description,
and other information) you may use `package_records_with_prefix`

    repo.package_records_with_prefix('apache')

This would return a hash such as the following:

  { 
    "apache2" => {
      "2.4.6-2" => 
      {
        "Package"=>"apache2",
        "Version"=>"2.4.6-2",
        "Installed-Size"=>"481",
        ...
      },
      "2.2.22-13" => 
      {
        "Package"=>"apache2",
        "Version"=>"2.2.22-13",
        "Installed-Size"=>"29",
        ...
       }
     }
   }

There is one entry for each package, which in turn has one entry for each
installable version of that package.

##Usage

Put the classes, types, and resources for customizing, configuring, and doing the fancy stuff with your module here. 

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
