# stack_install

Installs the packages listed in the OS-specific variable file selected by
`ansible_facts['os_family']`. `RedHat.yml` is used for RedHat-family systems,
and `Debian.yml` is used for Debian-family systems. Packages are installed
with Ansible's generic `package` module.

## Requirements and variables

- The target must be a supported RedHat- or Debian-family Linux distribution.
- Package names in the matching `vars/*.yml` file must exist in the target's
  enabled repositories.

This role does not configure or start the services; that is handled by
`stack_config`.

