# Ansible Role: Netbox

[![Netbox](
https://img.shields.io/badge/Netbox-v3.5.0-blue)](https://github.com/netbox-community/netbox)
[![CI](https://github.com/jvoss/ansible-role-netbox/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/jvoss/ansible-role-netbox/actions/workflows/ci.yml)
[![Netbox](https://github.com/jvoss/ansible-role-netbox/actions/workflows/netbox.yml/badge.svg)](https://github.com/jvoss/ansible-role-netbox/actions/workflows/netbox.yml)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-jvoss.netbox-blue.svg)](https://galaxy.ansible.com/jvoss/netbox)
[![Ansible Quality Score](https://img.shields.io/ansible/quality/56786?color=blue)](https://galaxy.ansible.com/jvoss/netbox)
[![Version](https://img.shields.io/github/release/jvoss/ansible-role-netbox.svg)](https://github.com/jvoss/ansible-role-netbox/releases/)

Installs, configures and maintains [NetBox](https://github.com/netbox-community/netbox)
on a variety of popular Linux distributions.

## Contents
- [Ansible Role: Netbox](#ansible-role-netbox)
  - [Contents](#contents)
  - [General Information](#general-information)
    - [Supported Platforms](#supported-platforms)
  - [Role variables](#role-variables)
  - [User accounts](#user-accounts)
    - [External Authentication](#external-authentication)
  - [Plugins](#plugins)
    - [Removing Plugins](#removing-plugins)
  - [Version locking](#version-locking)
  - [Dependencies](#dependencies)
  - [Example Playbook](#example-playbook)
  - [Contributing](#contributing)

## General Information

This role manages the installation and configuration of NetBox. It
does not provide PostgreSQL or Redis services that are required dependencies
of the application. Those tasks are intentionally left to allow the user to 
manage those services within their own roles and playbooks. Please see the 
[EXAMPLE](EXAMPLE.md) playbook for details about how to manage those services.

Releases are only issued when necessary to support changes in NetBox. This role 
is continuously tested on new versions of NetBox. Please see the 
[GitHub](https://github.com/jvoss/ansible-role-netbox) repo for the latest
compatability information.

### Supported Platforms

Tested on the following platforms:
* Amazon Linux 2
* CentOS 8
* Debian Buster (up to v3.1.11)
* Debian Bullseye
* Fedora Linux 37
* Rocky Linux 8 / Red Hat Enterprise Linux (RHEL) 8.2+
* Ubuntu 20.04
* Ubuntu 22.04

This role will require root access (via sudo) to manage system dependencies and actions
on behalf of netbox.

Supports NetBox versions 3+

## Role variables

Minimum required variables assuming `localhost` PostgreSQL and Redis services
are available:

```yaml
netbox_db_username: netbox
netbox_db_password: netbox
netbox_secret_key: "lnvRn_5Bypl8hBV4mMwgsMuHxr6uZvGwJyDqB7fcKqo"
```

If the `netbox_secret_key` is omitted a new one will be automatically generated
on each playbook run.

See [defaults/main.yml](defaults/main.yml) for a complete list of defaults and 
configurable options.

**Note**: Version 3.1+ introduced
[Dynamic Configuration Settings](https://netbox.readthedocs.io/en/stable/configuration/dynamic-settings/).
These configuration options may still be written to `configuration.py` preventing
modification via the UI. However, by default, this role *always* omits these
parameters unless `netbox_override_dynamic_config` is set to `True`. See 
[defaults/main.yml#L82](defaults/main.yml#L82) for details.

## User accounts

The following variables can be defined to create users during initial
installation only:

```yaml
netbox_superusers:
  - username: admin
    password: admin
    email: changeme@example.com
```

Each user requires a username, password and email address defined. The role will
attempt to create the defined users only once during initial installation. If 
`netbox_superusers` is not defined, no users are created and the manual user
creation process [documented](https://netbox.readthedocs.io/en/stable/installation/3-netbox/#create-a-super-user)
by Netbox can be used instead.

### External Authentication
See the [wiki](https://github.com/jvoss/ansible-role-netbox/wiki) for
information about available external authentication methods.

## Plugins 

Netbox plugins that are pip modules can be installed and configured by setting
the `netbox_plugins` list variable. Below is an example for the Netbox BGP
plugin.

```yaml
netbox_plugins:
  - name: netbox_bgp    # Plugin name
    pip: netbox-bgp     # Pip module name
    config:             # Plugin configuration
      device_ext_page: left
      asdot: True
```

### Removing Plugins
To remove a plugin, an `absent` state can be assigned to the `netbox_plugins`
entry:

```yaml
netbox_plugins:
  - name: netbox_bgp    # Plugin name
    pip: netbox-bgp     # Pip module name
    state: absent
```

**Note that it may be necessary to remove database tables that were installed
as part of a plugin.** This role does not manage database tables that may have
been created as part of a plugin. Please
[see the documentation](https://docs.netbox.dev/en/stable/plugins/#drop-database-tables)
for more information on table management.

## Version locking

A specific version of netbox can be configured using the variable:

```yaml
netbox_version_tag: v3.0.9
```

This tag should match the Github tag name for the release to be installed.
It will ensure that a specific target is maintained. If not set, each run will
attempt to find the latest release version to install.

*NOTE*: A version tag should be set for most environments to ensure a known
installation is maintained.

Another option is to deploy from a specifc branch and optionally a specific commit SHA

```yaml
netbox_install_method: git
netbox_git_branch: master
netbox_git_sha: 8f1acb700d72467ffe7ae5c8502422a1eac0693d # optional
```

## Dependencies

No Ansible dependencies. The application requires Redis and Postgres.

## Example Playbook

See [EXAMPLE](EXAMPLE.md) for a complete playbook example.

## Contributing

Contributions are encouraged. Please see [CONTRIBUTING](CONTRIBUTING.md) for
details.
