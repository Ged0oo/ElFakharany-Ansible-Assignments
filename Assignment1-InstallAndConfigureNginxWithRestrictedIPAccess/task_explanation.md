# Task: Install and configure Nginx with restricted IP access

This repository contains an Ansible playbook to install and configure Nginx on target hosts, with a simple site and an IP-based allow list.

Files in this folder:

- `install_nginx.yml` — Main Ansible playbook that:
  - Installs `nginx` (using `dnf`).
  - Creates `/etc/nginx/sites-available` and `/etc/nginx/sites-enabled`.
  - Deploys `nginx_default.conf.j2` to `/etc/nginx/sites-available/default` and enables it.
  - Ensures Nginx is running and reloads it on configuration changes.
  - Uses the variable `allowed_ips` to manage allowed client IPs in the template.
- `nginx_default.conf.j2` — Jinja2 template for the Nginx site configuration.
- `hosts.ini` — (optional) inventory file for Ansible (committed or local copy).

How to run:

1. Ensure you have an inventory file listing your `webservers` group (example: `hosts` or `hosts.ini`).
2. Run the playbook:

```bash
ansible-playbook -i hosts.ini install_nginx.yml
```

Notes and recommendations:

- Consider keeping any sensitive or environment-specific inventory files out of version control. A sample `hosts` file is included in this repo but is ignored by `.gitignore` to avoid accidental commits.
- Verify the Nginx configuration on target hosts with `nginx -t` (the playbook already runs this check).
- If you use SSH keys or vault secrets, ensure those files are not committed.

If you want, I can also commit these files to git or add a sample `hosts` for a specific environment.
