#apachex

This is very early development. Do not use it in production!

####Table of Contents

1. [Overview](#overview)
2. [Module Description - What the module does and why it is useful](#module-description)
3. [Setup - The basics of getting started with [Modulename]](#setup)
    * [What [Modulename] affects](#what-[apachex]-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with [Modulename]](#beginning-with-[Modulename])
4. [Usage - Configuration options and additional functionality](#usage)
5. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
5. [Limitations - OS compatibility, etc.](#limitations)
6. [Development - Guide for contributing to the module](#development)

##Overview

A one-maybe-two sentence summary of what the module does/what problem it solves. This is your 30 second elevator pitch for your module. Consider including OS/Puppet version it works with.       

##Module Description

If applicable, this section should have a brief description of the technology the module integrates with and what that integration enables. This section should answer the questions: "What does this module *do*?" and "Why would I use it?"
    
If your module has a range of functionality (installation, configuration, management, etc.) this is the time to mention it.

##Setup

###What [Modulename] affects

* A list of files, packages, services, or operations that the module will alter, impact, or execute on the system it's installed on.
* This is a great place to stick any warnings.
* Can be in list or paragraph form. 

###Setup Requirements **OPTIONAL**

If your module requires anything extra before setting up (pluginsync enabled, etc.), mention it here. 

###Beginning with [Modulename]

The very basic steps needed for a user to get the module up and running. 

If your most recent release breaks compatibility or requires particular steps for upgrading, you may wish to include an additional section here: Upgrading (For an example, see http://forge.puppetlabs.com/puppetlabs/firewall).

##Usage

Put the classes, types, and resources for customizing, configuring, and doing the fancy stuff with your module here. 

##Reference

### Class: `apachex::package`

This class represents apache package to install on the target OS.

**Note**: on FreeBSD we assume, that ports are used to manage apache package.
Other providers are not supported. You should either set the default package
provider to be `ports` or set `provider` parameter here to be `ports`.
Otherwise your manifests may stop working (`$build_options` and `$mpm` will
be ignored in best case, worse things can happen in other cases).

#### Parameters

Most parameters are passed directly to the
[package](http://docs.puppetlabs.com/references/latest/type.html#package)
resource, so they have exactly same meaning and syntax as for the package
resource. Defaults set by `Package { foo => bar }` are fully honored. Here we
mention only the affected package's parameters and new parameters introduced
by `apachex::package`


  - `auto_deinstall`

    Some changes, such as changing MPM on FreeBSD/ports, require to
    de-install currently installed package (e.g. www/apache22) and
    install a new package (e.g. www/apache22-worker-mpm). The
    `auto_deinstall` parameter, if set to `true`, enables automatic
    de-installation of currently installed apache package when necessary.
    By default it is set to `false` for safety, and any de-installation has to
    be done manually by user.

  - `bsd_ports_dir`

    Relevant only on BSD systems. Defines location of the ports tree.
    Defaults to `/usr/ports` on FreeBSD and OpenBSD and `/usr/pkgsrc` on
    NetBSD and to `undef` on other systems.

  - `bsd_port_dbdir`

    Relevant only on BSD systems. Defines directory where the results of
    configuration OPTIONS are stored. Defaults to `/var/db/ports`.

  - `build_options`

    Options used when the apache package is built (for example in FreeBSD
    ports packages are built on target machine and are customizable via build
    options). The format of this argument depends on the agent's system.

    **Note**: On FreeBSD/ports `build_options` can be applied fully only if
    the apache package is initially absent (or is going to be reinstalled 
    by puppet due to some other reasons). If apache is already installed
    and only the `build_options` have changed in your manifest, the new options
    will be saved to options' file (/var/db/ports/xxx/options), but the
    package will not be reinstalled with new configuration (so, still old
    configuration will be in use). This behavior may be changed in future.
    Currently reinstallation is left to user to be done "manually". In
    simplest case it may be done manually by manipulating puppet manifests
    as follows: set `ensure=>absent` for apachex::package, apply your
    manifest, then set new `options` and `ensure` and apply the manifest
    again.

  - `ensure`

    This has merely same effect as the *ensure* parameter to the `package`
    resource. The difference here is an enhanced versioning. If you pass
    "two-digit" version number (`2.2` for example), it shall still work, even
    if the underlying `package`'s provider is not versionable. Exact version
    numbers (for example `2.4.6-2`) are supported only by versionable
    providers.

    If you pass `2.X` style version number to fully versionable
    `apachex::package`, it will install most recent `2.X` apache package
    available in repositories. In case the apache package is already
    installed, the installation (`$ensure => '2.X'`) is triggered only if the
    installed version does not match the version in `$ensure`. For example,
    if `$ensure == '2.4'` and the installed version is `2.4.6-2`, no upgrade
    will be triggered, even if there is newer version in package repository.
    The upgrade (reinstall) will be triggered, however, if `$ensure == '2.4'`
    and the installed version is, for example, `2.2.22-13`.

    Note, that on some systems, migrations between '2.X' and '2.Y' would
    fail. For example, packages providing apache modules may depend on
    particular (installed) version of apache. Some package managers are not
    smart enough to handle the changes in dependencies and reinstall
    appropriate versions of modules automatically.


  - `mpm`

    MPM module to be used by apache. The list of all possible values is
    `event`, `itk`, `peruser`, `prefork`, `worker`. The list of supported
    values depends on agent's OS. This parameter is important only, when
    the selected apache doesn't support loadable MPM modules (in which case
    we must chose appropriate package with compiled-in MPM module).
    Apache `2.4` and later support loadable MPMs.

    **Note**: on FreeBSD the `mpm` parameter works in different ways for
    apache 2.2 and for apache >= 2.4. In the later case (>=2.4) you may need to
    uninstall apache manually in order to apply changes in `mpm`. If you don't,
    the new MPM will probably not be set (as a default) for apache package and
    there will be no warning about this.

  - `mpm_shared`

    Whether to enable MPM as loadable module (DSO). Defaults to true.
    Relevant only for apache >= 2.4 on systems, where pre-compiled
    packages are not used (FreeBSD ports, for example).

#### Variables

  - `::osfamily`
    Fact from facter

  - `::operatingsystem`
    Fact from facter

  - `::apachex_installed_version`
    Fact added by ptomulik-apachex module

  - `::apachex_repo_versions`
    Fact added by ptomulik-apachex module

#### Examples

Use all default parameters

  class { 'apachex::package': }

Stick to '2.2' line of apache. This shall work even with non-versionable
package providers:

  class { 'apachex::package': ensure => '2.2' }

Require particular version from repository. This works only with versionable
package providers:

  class { 'apachex::package': ensure => '2.2.13-1' }



##Limitations

This is where you list OS compatibility, version compatibility, etc.

##Development

Since your module is awesome, other users will want to play with it. Let them know what the ground rules for contributing are.

##Release Notes/Contributors/Etc **Optional**

If you aren't using changelog, put your release notes here (though you should consider using changelog). You may also add any additional sections you feel are necessary or important to include here. Please use the `## ` header. 
