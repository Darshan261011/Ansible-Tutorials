# Real World Project: Configure Two Ubuntu Web Servers

## Goal

Use Ansible to configure two Ubuntu servers as nginx web servers.

Tasks:

- Install nginx.
- Create a custom `index.html`.
- Create a Linux user.
- Install Git.
- Copy application files.
- Configure basic firewall rules.
- Restart nginx using a handler.
- Use variables.
- Use a Jinja2 template.
- Use role-based structure.
- Use Ansible Vault for a secret variable.

## Final Folder Structure

```text
webserver-project/
  ansible.cfg
  inventory.ini
  site.yml
  group_vars/
    web.yml
    vault.yml
  roles/
    webserver/
      defaults/
        main.yml
      tasks/
        main.yml
      handlers/
        main.yml
      templates/
        index.html.j2
        nginx-site.conf.j2
      files/
        app.txt
```

## Inventory

`inventory.ini`:

```ini
[web]
web1 ansible_host=192.168.56.11 ansible_user=ubuntu
web2 ansible_host=192.168.56.12 ansible_user=ubuntu
```

## ansible.cfg

```ini
[defaults]
inventory = inventory.ini
remote_user = ubuntu
host_key_checking = False
retry_files_enabled = False

[privilege_escalation]
become = True
become_method = sudo
become_user = root
```

## Variables

`group_vars/web.yml`:

```yaml
app_user: deploy
app_name: demo-web
nginx_port: 80
server_name: "_"
firewall_allowed_ports:
  - "22"
  - "80"
```

Create encrypted secrets:

```bash
ansible-vault create group_vars/vault.yml
```

Example vault contents:

```yaml
vault_app_secret: change-this-secret
```

Run playbooks with:

```bash
ansible-playbook site.yml --ask-vault-pass
```

## Role Defaults

`roles/webserver/defaults/main.yml`:

```yaml
nginx_package: nginx
nginx_service: nginx
```

## Role Tasks

`roles/webserver/tasks/main.yml`:

```yaml
---
- name: Install required packages
  ansible.builtin.apt:
    name:
      - nginx
      - git
      - ufw
    state: present
    update_cache: true

- name: Create application user
  ansible.builtin.user:
    name: "{{ app_user }}"
    shell: /bin/bash
    create_home: true

- name: Create application directory
  ansible.builtin.file:
    path: /opt/{{ app_name }}
    state: directory
    owner: "{{ app_user }}"
    group: "{{ app_user }}"
    mode: "0755"

- name: Copy application file
  ansible.builtin.copy:
    src: app.txt
    dest: /opt/{{ app_name }}/app.txt
    owner: "{{ app_user }}"
    group: "{{ app_user }}"
    mode: "0644"

- name: Deploy nginx site config
  ansible.builtin.template:
    src: nginx-site.conf.j2
    dest: /etc/nginx/sites-available/default
    mode: "0644"
  notify: Restart nginx

- name: Deploy index page
  ansible.builtin.template:
    src: index.html.j2
    dest: /var/www/html/index.html
    mode: "0644"
  notify: Restart nginx

- name: Allow firewall ports
  community.general.ufw:
    rule: allow
    port: "{{ item }}"
  loop: "{{ firewall_allowed_ports }}"
  when: ansible_facts['distribution'] == "Ubuntu"

- name: Enable firewall
  community.general.ufw:
    state: enabled
  when: ansible_facts['distribution'] == "Ubuntu"

- name: Ensure nginx is running
  ansible.builtin.service:
    name: "{{ nginx_service }}"
    state: started
    enabled: true
```

Install firewall collection:

```bash
ansible-galaxy collection install community.general
```

## Handlers

`roles/webserver/handlers/main.yml`:

```yaml
---
- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
```

## Templates

`roles/webserver/templates/index.html.j2`:

```jinja
<!doctype html>
<html>
<head>
  <title>{{ app_name }}</title>
</head>
<body>
  <h1>{{ app_name }} deployed by Ansible</h1>
  <p>Host: {{ inventory_hostname }}</p>
  <p>Secret loaded: {{ vault_app_secret is defined }}</p>
</body>
</html>
```

`roles/webserver/templates/nginx-site.conf.j2`:

```jinja
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};
    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

## Site Playbook

`site.yml`:

```yaml
---
- name: Configure Ubuntu web servers
  hosts: web
  become: true
  vars_files:
    - group_vars/vault.yml
  roles:
    - webserver
```

## Commands

```bash
ansible all -m ping
ansible-playbook --syntax-check site.yml
ansible-playbook site.yml --ask-vault-pass --check --diff
ansible-playbook site.yml --ask-vault-pass
curl http://192.168.56.11
curl http://192.168.56.12
```

## Troubleshooting

- If SSH fails, test `ssh ubuntu@192.168.56.11`.
- If sudo fails, run with `--ask-become-pass`.
- If UFW module fails, install `community.general`.
- If Vault fails, verify the correct vault password.
- If nginx fails, run `sudo nginx -t` on the managed node.

## Mini Practice

- Add a third web server to inventory.
- Change the app name using `group_vars/web.yml`.
- Add HTTPS port `443` to the firewall list.
- Add a handler that reloads nginx instead of restarting it.

