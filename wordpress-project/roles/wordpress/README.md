# wordpress

Installs and configures WordPress after the packages and supporting services have been prepared.

## Behavior

The role is designed to:

- Create the WordPress database and database user.
- Download WP-CLI and WordPress core.
- Generate `wp-config.php`.
- Run WordPress installation steps.
- Install and activate plugins listed in the shared configuration.

## Requirements and variables

- Install the `community.mysql` collection on the Ansible control node with `ansible-galaxy collection install -r requirements.yml`.
- The managed host needs WP-CLI prerequisites and an available local MariaDB instance.
- `group_vars/common.yml` supplies the `wordpress`, `wp_database`, `wp_cli`, and `wordpress_plugins` settings.
- Encrypted Vault variables supply `vault_email`, `vault_wp_admin_password`, and `vault_wp_db_password`. Confirm that the Vault location used by the playbook exists on the current machine.
- The WordPress system account and directory settings come from the `wordpress_system` defaults in `stack_config`.


