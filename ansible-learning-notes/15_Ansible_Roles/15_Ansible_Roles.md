# Ansible Roles

## What Are Roles?

Roles are reusable playbook structures. A role groups tasks, handlers, templates, files, variables, defaults, and metadata.

Roles are useful when a playbook becomes large or when the same automation is needed across projects.

## Why Roles Are Used

- Cleaner project structure.
- Reusable automation.
- Easier testing and sharing.
- Better separation of tasks, templates, and variables.

## Role Directory Structure

```text
roles/
  nginx/
    tasks/
      main.yml
    handlers/
      main.yml
    templates/
      site.conf.j2
    files/
      index.html
    vars/
      main.yml
    defaults/
      main.yml
    meta/
      main.yml
```

## Create Role From Scratch

```bash
ansible-galaxy role init roles/nginx
```

## defaults

Defaults are low-precedence variables.

`roles/nginx/defaults/main.yml`:

```yaml
nginx_port: 80
server_name: example.local
```

## vars

Vars have higher precedence than defaults.

`roles/nginx/vars/main.yml`:

```yaml
nginx_package: nginx
nginx_service: nginx
```

## tasks

`roles/nginx/tasks/main.yml`:

```yaml
---
- name: Install nginx
  ansible.builtin.apt:
    name: "{{ nginx_package }}"
    state: present
    update_cache: true

- name: Deploy site config
  ansible.builtin.template:
    src: site.conf.j2
    dest: /etc/nginx/sites-available/default
  notify: Restart nginx

- name: Start nginx
  ansible.builtin.service:
    name: "{{ nginx_service }}"
    state: started
    enabled: true
```

## handlers

`roles/nginx/handlers/main.yml`:

```yaml
---
- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
```

## templates

`roles/nginx/templates/site.conf.j2`:

```jinja
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};
    root /var/www/html;
    index index.html;
}
```

## Use Role In Playbook

```yaml
---
- name: Apply nginx role
  hosts: web
  become: true
  roles:
    - nginx
```

Run:

```bash
ansible-playbook -i inventory.ini site.yml
```

## Mini Practice

- Create an `nginx` role.
- Move nginx tasks into the role.
- Add a handler.
- Add a template.
- Run the role from `site.yml`.

