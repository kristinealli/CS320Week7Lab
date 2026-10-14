# stack_config

Configures the services and operating-system settings required by the WordPress deployment after `stack_install` has installed the packages.

## Behavior

As described by the role's current tasks, it:

- Creates the WordPress system group, user, and document root.
- Deploys the NGINX site configuration and PHP-FPM pool configuration from templates.
- Updates PHP configuration settings such as `memory_limit`, `upload_max_filesize`, and `post_max_size`.
- Enables and starts NGINX, MariaDB, and PHP-FPM.
- Validates NGINX configuration with `nginx -t`.

## Requirements and variables

Role defaults in `defaults/main.yml` provide `wordpress_system` settings (user, group, home directory, comment, and state). The main playbook also loads shared settings from `group_vars/common.yml`, including:

- `php_fpm_service`, `php_fpm_pool_path`, and `php_fpm_socket`
- `php_ini_path`
- `nginx_user` and `nginx_service`
- `wordpress.domain`

The `Configure PHP settings` task uses `path: "{{ php_ini_path }}"`, so that variable must resolve to an existing PHP configuration file on each host. During troubleshooting, Ubuntu used `/etc/php/8.3/fpm/php.ini`; AlmaLinux used `/etc/php.ini`.

Review service names, sockets, paths, and rendered templates for each OS before deployment. `stack_install` must complete before this role runs.

## Verification

Check the play recap, service status, and `nginx -t`. A dry run can report missing users/groups when earlier creation tasks have not actually executed.
