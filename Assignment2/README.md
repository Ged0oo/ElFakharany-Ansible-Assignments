# Ansible Nginx Role Demo

## Overview
This demo shows a simple Ansible setup to install and configure Nginx using a role-based structure. It includes an inventory, a playbook, and a `nginx` role that demonstrates common Ansible patterns: idempotent tasks, templates, handlers, and role defaults.

## Repository Structure
- [hosts.ini](hosts.ini) — inventory for target hosts
- [install_nginx.yml](install_nginx.yml) — top-level playbook to run the role
- roles/
  - nginx/
    - defaults/main.yml — default variables for the role
    - handlers/main.yml — handlers (e.g., restart nginx)
    - tasks/main.yml — main task list to install/configure nginx
    - templates/nginx_default.conf.j2 — Jinja2 template for nginx config

## Demo Purpose
- Show how to structure a role for a service (Nginx).
- Demonstrate using templates and handlers to manage service configuration and restarts.
- Reinforce Ansible principles and best practices used in real projects.

## Requirements
- Ansible 2.9+ (or newer)
- Target hosts reachable via SSH and defined in `hosts.ini`
- Sudo/privilege escalation available on target hosts (use `become: true`) where required

## Usage
Run the playbook against the inventory:

```bash
ansible-playbook -i hosts.ini install_nginx.yml
```

To perform a dry-run (no changes applied):

```bash
ansible-playbook -i hosts.ini install_nginx.yml --check
```

If using become with a password, add `-K` to prompt for the privilege password.

## What the Playbook Does
- Installs the Nginx package using the appropriate package module (apt/yum depending on target).
- Places the Nginx site configuration from the Jinja2 template `roles/nginx/templates/nginx_default.conf.j2`.
- Notifies a handler to reload/restart Nginx only when the configuration changes.
- Ensures the Nginx service is enabled and running.

## Role Design & Files
- `defaults/main.yml`: keep default, safe-to-change variables here.
- `tasks/main.yml`: sequential, idempotent tasks (use modules not raw shell).
- `templates/`: Jinja2 templates for configuration files; use variables from `defaults` or inventory.
- `handlers/main.yml`: service restart/reload tasks triggered by `notify` when changes occur.

## Ansible Rules & Principles (Best Practices)
- Idempotence: write tasks so they can run multiple times without unwanted side effects.
- Use modules not shell/command: prefer `apt`, `yum`, `service`, `template`, `file`, etc.
- Roles: encapsulate functionality (defaults, tasks, handlers, templates) for reuse.
- Handlers & notify: trigger service reloads only when configs change to avoid unnecessary restarts.
- Variables scope: keep safe defaults in `defaults/` and sensitive or environment-specific values in `vars/` or inventory.
- Templates: use Jinja2 templates for config files to avoid brittle string manipulation.
- Avoid hard-coded values: use variables and group/host vars from inventory.
- Use `become`/privilege escalation explicitly when needed — avoid running everything as root by default.
- Use `check` mode and `--diff` during development to preview changes.
- Use tags for selective runs (e.g., `--tags "nginx,config"`).
- Use `handlers` for idempotent restarts and `changed_when`/`failed_when` only when necessary.
- Security: store secrets with Ansible Vault; do not commit sensitive data.
- Testing: test roles with `ansible-playbook --check`, molecule, or in a disposable VM/container.

## Testing & Verification
- After running the playbook verify service status on targets:

```bash
ansible -i hosts.ini all -m shell -a "ss -ltnp | grep nginx" --become
```

- Or use `curl` against the configured host to confirm HTTP responses (subject to network/firewall rules):

```bash
curl -I http://<target-ip>
```

## Notes & Next Steps
- To harden the role, add idempotent tests and molecule scenarios.
- For production, add CI checks, vault for secrets, and role versioning.

---

If you want, I can:
- Add a short CONTRIBUTING or USAGE section with example inventory values.
- Create a `molecule` test scaffold for this role.

