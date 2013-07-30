## Class: apache2::defaults

This class represents default settings of apache2 puppet module.

### Parameters

This class has no parameters.

[*sample_parameter*]
  Explanation of what this parameter affects and what it defaults to.
  e.g. "Specify one or more upstream ntp servers as an array."

### Variables

The apache2::defaults defines following variables:

[*$apache2::defaults::mpm*]
  Default MPM module used to run apache daemon. Type: string. 
  Value: `'prefork'`.

### Examples

 class { 'apache2::defaults' }

=== Authors

Paweł Tomulik <ptomulik@meil.pw.edu.pl>

=== Copyright

Copyright 2013 Paweł Tomulik.

ptomulik@tea:$ puppet doc manifests/defaults.pp 
== Class: apache2::defaults

This class represents default settings of apache2 puppet module.

=== Parameters

This class has no parameters.

[*sample_parameter*]
  Explanation of what this parameter affects and what it defaults to.
  e.g. "Specify one or more upstream ntp servers as an array."

=== Variables

The apache2::defaults defines following variables:

[*$apache2::defaults::mpm*]
  Default MPM module used to run apache daemon. Type: string. 
  Value: `'prefork'`.

=== Examples

 class { 'apache2::defaults' }

=== Authors

Paweł Tomulik <ptomulik@meil.pw.edu.pl>

=== Copyright

Copyright 2013 Paweł Tomulik.
