# PHP7 Nginx (`php7-nginx`)

Master: [![Build Status](https://semaphoreci.com/api/v1/bas-ansible-roles-collection/php7-nginx/branches/master/badge.svg)](https://semaphoreci.com/bas-ansible-roles-collection/php7-nginx)
Develop: [![Build Status](https://semaphoreci.com/api/v1/bas-ansible-roles-collection/php7-nginx/branches/develop/badge.svg)](https://semaphoreci.com/bas-ansible-roles-collection/php7-nginx)

Bridging role to use PHP 7 with Nginx using PHP-FPM

**Part of the BAS Ansible Role Collection (BARC)**

**This role uses version 0.3.2 of the https://github.com/bas-ansible-roles-collection/php7-nginx.git flavour of the BAS Base Project - Pristine**.

## Overview

* Installs the latest stable version of PHP-FPM from non-system, or optionally, from system only sources
* Configures the PHP configuration file for the CLI SAPI with safe and secure defaults
* Configures the default PHP-FPM pool to be compatible with each supported operating system
* Configures Nginx server blocks to use PHP-FPM for processing relevant resources (i.e. PHP files)

## Quality Assurance

This role uses manual and automated testing to ensure its features work as advertised.
See [here](tests/README.md) for more information.

## Ansible compatibility

* this role supports Ansible 1.8 or higher in the 1.x series
* this role supports Ansible 2.x

**Note:** Support for Ansible 1.x is deprecated by this role, future versions will support Ansible 2.x only.

More information on Ansible compatibility is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Ansible-compatbility).

## Dependencies

* [**bas-ansible-roles-collection.php7**](https://galaxy.ansible.com/bas-ansible-roles-collection/php7/)
  * Minimum version: *0.1.0*
* [**bas-ansible-roles-collection.nginx**](https://galaxy.ansible.com/bas-ansible-roles-collection/nginx/)
  * Minimum version: *2.1.0*

More information on role dependencies is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-dependencies)

## Requirements

* none

More information on role requirements is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-requirements)

## Limitations

* Some variables in this role cannot be easily overridden

This specifically affects: *php7_nginx_fpm_www_pool_socket* and *php7_nginx_fpm_www_pool_options*.

Values for these variables vary on each supported operating system and therefore cannot be defined as variables in 
`defaults/main.yml` (which are universal). Ansible does not support conditionally importing additional variables at 
the same priority of role 'defaults' (i.e. variables defined in `defaults/main.yml`), therefore these variables must be 
set in `vars/` within this role, and conditionally loaded using the 
[include_vars module](http://docs.ansible.com/ansible/include_vars_module.html).

Variables set at this priority cannot be easily overridden in playbooks (i.e. using the `vars` option), or in variable 
files (i.e. using the `vars_files` option). In fact only 'extra_vars' set on the command line can override variables of
this precedence.

Given the nature of these variables, it is not expected likely users will need (or want) to changes the values for these
variables, and therefore the difficultly needed to override them is considered an acceptable, and not significant 
limitation. However, if other variables need to be defined in this way this may need to revisited in the future.

*This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued.*
*Pull requests to address this will be considered.*

See the 
[Ansible Documentation](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) 
for further details on variable precedence.

See [BARC-93](https://jira.ceh.ac.uk/browse/BARC-93) for further details.

* On CentOS the FPM SAPI cannot be configured using a typical php.ini file

The configuration file used by the FPM SAPI is set when PHP is compiled, and offers no configuration option to change 
this (presumably for security reasons). As we do not want to change the default PHP configuration file, or configure 
PHP to read from a non-default file (since this would just become the new default file just with a non-default name),
we need to adopt a different approach.

Within PHP-FPM, pools can set PHP configuration options, on a per-pool basis. This role will configure the default 
`www` pool in this way, setting the options we need. Since there typically aren't that many options being set this is
considered acceptable for the time being.

Whilst this works it introduces a new limitation in that users are forced to use the default FPM pool, unless they also 
set any PHP configurations options in additional pools as well. Since this role intentionally does not modify 
non-default FPM pools, this is left to the end-user to perform.

On Ubuntu PHP-FPM uses a `/etc/php5/fpm/php.ini` file as per its conventions (i.e. `/etc/php5/[SAPI]/php.ini`) and so
this is not a problem.

*This limitation is considered to be significant. Solutions will be actively pursued.*
*Pull requests to address this will be gratefully considered and given priority.*

See [BARC-106](https://jira.ceh.ac.uk/browse/BARC-106) for further details.

More information on role requirements is available in the 
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-limitations)

* FPM SAPI configuration options are not used on non-default FPM pools using CentOS

This is a consequence of CentOS not having support for a FPM specific PHP configuration file. Instead options need to 
be expressed in FPM pool configuration files.

As this role only configures the default FPM pool, if additional pools are used these PHP options will not be applied.
This role will intentionally not configure non-default FPM pools, therefore the user will need to ensure these options
are applied outside this role.

This is possible by replicating the relevant tasks performed by this role for the default pool.

*This limitation is **NOT** considered to be significant. Solutions will NOT be actively pursued.*
*Pull requests to address this will **NOT** be considered.*

See [BARC-107](https://jira.ceh.ac.uk/browse/BARC-107) for further details.

More information on role limitations is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-limitations)

## Usage

### BARC Manifest

By default, BARC roles will record that they have been applied to a system, this is termed the BAS Manifest.
The specific local facts set for this role are:

* `ansible_local.barc_php7_nginx.general.role_applied` - (boolean) records whether a role has been applied
* `ansible_local.barc_php7_nginx.general.role_version` - (string) records the version of the role that was applied

**Note:** You **SHOULD** use this feature to determine whether this role has been applied to a system.
If you do not want these facts to be set by this role, you **MUST** skip the **BARC_SET_MANIFEST** tag.

More information is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-Manifest)

### What are bridging-roles?

Bridging-roles are a concept with the BARC for roles which enable two or more roles to be used together.

They are used to keep roles more generic and prevent roles needing to be aware of other roles. They also act as a
convenience as specifying the bridging role in a playbook will, through role dependencies, include the various roles it
'bridges'.

More information is available in the 
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Bridging-roles)

### Non-system packages

It is a convention of BARC roles to use the latest version of packages. Where a suitable non-system package source is 
available it will be used. Suitable non-system packages require a reputable, maintainer, typically a company or well 
respected individual. If this is for some reason unsuitable, it is possible to only use system packages by setting the 
*BARC_use_non_system_package_sources* variable to `false`.

Note: As the package policy varies between system and non-system package sources, and between operating systems, the 
version of installed packages is variable.

### Coexistence with PHP 5

Coexistence with PHP 5 is not supported by this role. I.e. Applying the `php5-nginx` role alongside this role is not 
supported. Instead it is assumed machines will run either PHP 5 **OR** PHP 7.

Upgrading from PHP 5 to PHP 7 is not supported by this role. Instead it is assumed machines will rebuilt with the new
version.

These issues are considered a limitation, see the *Limitations* section for more information.

### PHP configuration files

This role will only configure options relevant to the `fpm` SAPI.

See the [php7](https://galaxy.ansible.com/bas-ansible-roles-collection/php7/) role, on which this role depends, 
for more information on PHP configuration files.

Settings for other SAPIs, such as the CLI, **SHOULD** be set in other roles, or project/purpose specific playbooks.
Providing the Ansible INI module is used, setting additional INI options will not cause conflicts with this role and 
so preserve idempotency.

The configuration options set by this role are considered suitably generic and applicable to all situations such that 
they are safe to be used as defaults - however it is accepted there are situations they would not be suitable. They 
include options for:

* Processing time and memory limits
* Default timezone - localised to *Europe/London*
* Error reporting and logging
* Hiding the PHP version for security purposes

See the *php5_nginx_sapi_fpm_options* variable in the role defaults file (`defaults/main.yml`) for the specific options 
set. Where any of these options are unsuitable, override this variable with a copy of these defaults, omitting the 
unsuitable options.

On CentOS there is no FPM specific PHP configuration file available, and it is not possible to create one.
Instead PHP configurations are set in the default FPM pool configuration file using `php_value` and `php_flag`.
This requires a 'type' (value or flag) to be stored for each PHP configuration option, which this role will use to
select the appropriate configuration directive. See the *FPM pools* sub-section for more information on FPM pools.

This is considered a limitation, see the *Limitations* section for more information.

### PHP extensions

See the [php7](https://galaxy.ansible.com/bas-ansible-roles-collection/php7/) role, on which this role depends, 
for more information on which extensions are enabled and how to control them.

### Nginx

This role will only configure Nginx to support integrating with PHP-FPM.

See the [Nginx](https://galaxy.ansible.com/bas-ansible-roles-collection/nginx/) role, on which this role depends, 
for more information on how Nginx is installed and configured more generally.

This role will create additional configuration files intended to be loaded within server blocks. These additional
configuration files will be loaded automatically by server block templates provided by the Nginx role, if used.

Additional configuration files included in this role:

* In server block templates:
  * `php-fpm.conf` - Proxies requests to PHP-FPM via the Fast CGI protocol for relevant requests (i.e. for PHP pages)
  * `fastcgi-params.conf` - Maps HTTP headers to Fast CGI parameters

More information on these additional sections may be provided in other sub-sections in this README.

### FPM pools

PHP-FPM uses the concept of a pool to define different options, such as resources or security options for different 
websites or applications used in a single web-server, or web-server virtual host.

Each pool uses a separate pool configuration file, which can also contain options which affect PHP, but only within
each pool.

This role will configure the default pool, *www* only. This pool is configured to use a UNIX socket for accessing FPM
with the relevant permissions.

On CentOS systems FPM SAPI specific options are also set as it is not possible to do this using a dedicated PHP 
configuration file. See the *PHP configuration files* sub-section for more information.

### Typical playbook

```yaml
---

- name: install PHP 7, Nginx and PHP-FPM
  hosts: all
  become: yes
  vars: []
  roles:
    - bas-ansible-roles-collection.php7-nginx
```

### Tags

BARC roles use standardised tags to control which aspects of an environment are changed by roles.
Tasks in this role will 'tag' themselves with these tags as appropriate, typically in `tasks/main.yml`.

More information is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Appendix-B---BARC-Standardised)

### Variables

#### *php7_nginx_barc_role_name*

* **MUST NOT** be specified
* specifies the name of this role within the BAS Ansible Roles Collection (BARC) used for setting local facts
* see the *BARC roles manifest* section for more information
* example: `php7_nginx`

#### *php7_nginx_barc_role_version*

* **MUST NOT** be specified
* specifies the name of this role within the BAS Ansible Roles Collection (BARC) used for setting local facts
* see the *BARC roles manifest* section for more information
* example: `2.0.0`

### *BARC_use_non_system_package_sources*

* **MAY** be specified
* Specifies whether non-system sources can be used to install packages
* Note: This variable is scoped to all other BARC roles which install packages from non-system sources
* Values MUST use one of these options, as determined by Ansible:
    * `true`
    * `false`
* Values **SHOULD NOT** be quoted to prevent Ansible coercing values to a string
* Where not specified, a value of `true` will be assumed
* Default: `true`

### *webserver_virtual_hosts_document_indexes*

* **MAY** be specified
* Specifies a list of files that will be used as an index document
* Structured as a list of items, with each item having the following properties:
    * Item values **MUST** be valid file names, including any file extension, as determined by the web server
* The default item values, are conventional defaults, other values **SHOULD NOT** be used without good reason
* Defaults:
```
- index.html
- index.php
```

#### *php7_nginx_fpm_www_pool_socket*

* **MAY** be specified
* Specifies the location to create the FPM UNIX socket
* Values MUST be a valid system socket path, as determined by the operating system
* Values **MUST** use absolute paths
* The default value, which varies depending on the machine operating system, is a conventional default, other values
**SHOULD NOT** be used without good reason
* The default value for this variable is set as a role variable, rather than a role default variable, this has 
implications if overriding this value, see the *Limitations* section for more information
* Default:
    * `/var/run/php-fpm/php-fpm.sock` - CentOS
    * `/var/run/php-fpm.sock` - Ubuntu

#### *php7_nginx_fpm_www_pool_options*

**MAY** be specified.

Specifies configuration options for the FPM default pool ('www').

Structured as a list of items, with each item having the following properties:

* Section
  * **MUST** be specified
  * Specifies the section of the INI configuration file options for this item belong to
  * The section maps to the FPM pool to be configured
  * Values **MUST** be valid pool identifiers as determined by PHP-FPM
  * The default value for this variable is a conventional default, other values **SHOULD NOT** be used without good 
  reason
  * Example: `www`
* Options
    * **MAY** be specified
    * Specifies the list of options, and values, to be set within the section (pool) set by the *section* item
    * Structured as a list of sub-items, with each sub-item having the following properties:
      * *option*
            * **MUST** be specified if any part of the sub-item is specified
            * Specifies the *option* of the pool option/value pair
            * Values **MUST** be valid option names as determined by the INI configuration format and **MUST** be valid
            option names as determined by PHP-FPM
        * *value*
            * **MUST** be specified if any part of the sub-item is specified
            * Specifies the *value* of the pool option/value pair
            * Values **MUST** be valid values as determined by the INI configuration format and **MUST** be valid
            option names as determined by PHP-FPM
            * Boolean values **MUST** be quoted to prevent Ansible coercing values to True/False which is invalid for 
            PHP configurations

The default value, which varies depending on the machine operating system, is a conventional default, other values
**SHOULD NOT** be used without good reason.

The default value for this variable is set as a role variable, rather than a role default variable, this has 
implications if overriding this value, see the *Limitations* section for more information.

Example:

```yml
php7_nginx_fpm_www_pool_options:
  - section: "www"
    options:
      - option: listen.owner
        value: www-data
```

Default:
  * See `vars/CentOS.yml` - CentOS
  * See `vars/Ubuntu.yml` - Ubuntu

### *php7_nginx_sapi_fpm_options*

**MAY** be specified.

Specifies configuration options for the FPM (PHP-FPM) SAPI.

Structured as a list of items, with each item having the following properties:

* *section*
    * **MUST** be specified
    * Specifies the section of the INI configuration file options for this item belong to
    * Values **MUST** be valid section names as determined by the INI configuration format
* *options*
    * **MAY** be specified
    * Specifies the list of options, and values, to be set within the section set by the *section* item
    * Structured as a list of sub-items, with each sub-item having the following properties
        * *option*
            * **MUST** be specified if any part of the sub-item is specified
            * Specifies the *option* of the INI option/value pair
            * Values **MUST** be valid option names as determined by the INI configuration format and **MUST** be valid
            option names as determined by PHP
        * *value*
            * **MUST** be specified if any part of the sub-item is specified
            * Specifies the *value* of the INI option/value pair
            * Values **MUST** be valid values as determined by the INI configuration format and **MUST** be valid
            option names as determined by PHP
            * Boolean values **MUST** be quoted to prevent Ansible coercing values to True/False which is invalid for 
            PHP configurations
        * *type*
          * **MUST** be specified if any part of the sub-item is specified
          * Specifies whether the *value* for each sub-item is a 'value' (such as '12') or a 'flag' (such as ON)
          * This is required for choosing the appropriate PHP-FPM pool directive for setting PHP options on CentOS
          * Values **MUST** be valid values as determined by PHP-FPM
          * Values **MUST** use one of these options, as determined by PHP-FPM
            * `value`
            * `flag`

Example:

```yml
php7_nginx_sapi_fpm_options:
  - section: "Resource Limits"
    options:
      - option: max_execution_time  # (seconds)
        value: 30
```

Default: *See role defaults*

## Developing

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the
[BAS Ansible Roles Collection](https://jira.ceh.ac.uk/projects/BARC) (BARC) project on Jira.

This service is currently only available to BAS or NERC staff, although external collaborators can be added on request.
See our contributing policy for more information.

More information is also available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Issue-Tracking)

### Source code

All changes should be committed, via pull request, to the canonical repository:

`ssh://git@stash.ceh.ac.uk:7999/barc/php7-nginx.git`

A read-only mirror of this repository is maintained on GitHub:

`git@github.com:bas-ansible-roles-collection/php7-nginx.git`

More information is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Source-Code)

### Contributing policy

This role welcomes contributions, see `CONTRIBUTING.md` for our general policy.

### Release procedure

The general release procedure for BARC roles is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Release-procedures)

## Licence

Copyright 2016 NERC BAS.

Unless stated otherwise, all documentation is licensed under the Open Government License - version 3.
All code is licensed under the MIT license.

Copies of these licenses are included within this role.
