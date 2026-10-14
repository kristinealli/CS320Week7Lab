# CS-320 Week 7 — Ansible WordPress Deployment

This project uses Ansible to deploy a WordPress stack to the managed Linux hosts listed in `hosts.ini`. The lab is being adapted and tested for **Ubuntu (Debian family)** and **AlmaLinux (Red Hat family)**.

The playbook `site.yml` calls three roles in order:

1. `stack_install` — installs OS-specific packages.
2. `stack_config` — configures the WordPress account and directory, NGINX, PHP-FPM, PHP settings, and services.
3. `wordpress` — provisions the database, installs WordPress using WP-CLI, and manages configured plugins.

## Repository layout

```text
wordpress-project/
├── hosts.ini
├── site.yml
├── requirements.yml
├── group_vars/
│   ├── common.yml
│   └── all/vault.yml       # optional local encrypted Vault location; see below
├── host_vars/             # optional per-host settings
├── roles/
│   ├── stack_install/
│   ├── stack_config/
│   └── wordpress/
└── evidence/              # lab output and screenshots, as appropriate
```

The exact Vault location depends on the configuration currently in use. Earlier versions of the lab also used `group_vars/vault.yml` explicitly in `site.yml`. **Use one consistent source of encrypted secrets, and verify the path before running commands.**

## Prerequisites

- Ansible installed on the control machine (WSL/Linux in the lab).
- SSH connectivity to both managed servers and accounts with sudo access.
- Package repositories providing the packages named in `roles/stack_install/vars/Debian.yml` and `RedHat.yml`.
- The `community.mysql` collection:

  ```bash
  ansible-galaxy collection install -r requirements.yml
  ```

- Encrypted WordPress credentials and privilege-escalation passwords supplied through Ansible Vault as configured locally.

## Inventory and variables

Review `hosts.ini` before running. The lab uses inventory hostnames `kj_cs320_ubuntu20` (Ubuntu) and `tp_cs320_al10` (AlmaLinux). Confirm their addresses, SSH users, and keys against your actual VM names.

`group_vars/common.yml` defines non-secret shared WordPress and service settings. Sensitive settings (including `vault_email`, `vault_wp_admin_password`, `vault_wp_db_password`, and any stored sudo passwords) belong in the **ecrypted Vault**.

For separate host sudo passwords, host-specific settings can refer to Vault variables:

```yaml
# host_vars/kj_cs320_ubuntu20.yml
ansible_become_password: "{{ vault_ubuntu_sudo_password }}"
```

```yaml
# host_vars/tp_cs320_al10.yml
ansible_become_password: "{{ vault_alma_sudo_password }}"
```

## Run the lab

Run from the directory containing `site.yml`:

```bash
ansible-inventory -i hosts.ini --graph
ansible -i hosts.ini all -m ping
ansible-playbook -i hosts.ini site.yml --syntax-check --ask-vault-pass
ansible-playbook -i hosts.ini site.yml --check --diff --ask-vault-pass
ansible-playbook -i hosts.ini site.yml --ask-vault-pass
```


After a successful deployment, run the playbook a second time to assess idempotence:

```bash
ansible-playbook -i hosts.ini site.yml --ask-vault-pass
```

## OS-specific notes

- `stack_install` selects package lists by `ansible_facts['os_family']`.
- PHP configuration paths differ. Verify that `php_ini_path` resolves to the correct path for each host.
- NGINX, PHP-FPM service names, pool paths, and sockets also differ. 

## Security 

Do not commit Vault password files, private SSH keys, plaintext credentials, or generated logs containing secrets. Check `.gitignore`, and review `git status` before committing. Git-ignored Vault files should be transferred securely and configured separately on another machine.
