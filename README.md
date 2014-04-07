#GnuPG puppet module

**This is a modification of the golja/gnupg module, because it is not accepting www-data as a valid user name**

I have only modified the regular expression for the validation.

##Overview

Install GnuPG on Ubuntu/Debian/RedHat/CentOS/Amazon AMI and manage users public keys.

Tested with Tavis CI

##Installation

     $ puppet module install dereklio/gnupg

##Usage

####Install GnuPG package

    include '::gnupg'

####Add public key 20BC0A86 from PGP server from hkp://pgp.mit.edu/ to user root

```puppet
gnupg_key { 'hkp_server_20BC0A86':
  ensure     => present,
  key_id     => '20BC0A86',
  user       => 'root',
  key_server => 'hkp://pgp.mit.edu/',
}
```

####Add public key D50582E6 from standard http URI to user foo

```puppet
gnupg_key { 'jenkins_foo_key':
  ensure     => present,
  key_id     => 'D50582E6',
  user       => 'foo',
  key_source => 'http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key',
}
```

####Add public key D50582E6 from puppet fileserver to user foo

```puppet
gnupg_key { 'jenkins_foo_key':
  ensure     => present,
  key_id     => 'D50582E6',
  user       => 'foo',
  key_source => '"puppet:///modules/gnupg/D50582E6.key",',
}
```

####Remove public key 20BC0A86 from user root

```puppet
gnupg_key {'root_remove':
  ensure => absent,
  key_id => '20BC0A86',
  user   => 'root',
}
```

###Parameters

####gnupg

#####`package_ensure`

Valid value present/absent. In most cases you should never uninstall this package,
because most of the modern Linux distros rely on gnupg for package verification, etc
Default: present

#####`package_name`

Name of the GnuPG package. Default value determined by $::osfamily/$::operatingsystem facts

####gnupg_key

#####`ensure`

**REQUIRED** - Valid value present/absent

#####`user`

**REQUIRED** - System username for who to store the public key. Also define the location of the
pubring (default ${HOME}/.gnupg/)

#####`key_id`

**REQUIRED** - Key ID. Usually the traditional 8-character key ID. Also accepted the
long more accurate (but  less  convenient) 16-character key ID. Accept only hexadecimal
values.

#####`key_source`

**REQUIRED** if `key_server` is not defined and `ensure` is present.
A source file containing PGP key. Values can be URIs pointing to remote files,
or fully qualified paths to files available on the local system.

The available URI schemes are *puppet*, *https*, *http* and *file*. *Puppet*
URIs will retrieve files from Puppet's built-in file server, and are
usually formatted as:

puppet:///modules/name_of_module/filename

#####`key_server`

**REQUIRED** if `key_source` is not defined and `ensure` is present.

PGP key server from where to retrieve the public key. Valid URI schemes are
*http*, *https*, *ldap* and *hkp*.

### Tests

There are two types of tests distributed with the module. Unit tests with rspec-puppet and system tests using rspec-system.

For unit testing, make sure you have:

* rake
* bundler

Install the necessary gems:

    bundle install --path=vendor

And then run the unit tests:

    bundle exec rake spec


If you want to run the system tests, make sure you also have:

* vagrant > 1.3.x
* Virtualbox > 4.2.10

Then run the tests using:

    bundle exec rake spec:system

To run the tests on different operating systems, see the sets available in .nodeset.yml and run the specific set with the following syntax:

    RSPEC_SET=debian-607-x64 bundle exec rake spec:system

##Credits
This module design, tests, etc are based on current puppetlabs- and sensu- modules and the help received via #puppet and #puppet-dev, especially from _rc and ken_barber

## Supported Platforms

* Debian Wheezy
* Ubuntu 12+
* RedHat 5/6
* CentOS 5/6
* Amazon AMI

## License

See LICENSE file

