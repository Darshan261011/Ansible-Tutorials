# Modules Deep Dive

## What Are Modules?

Modules are Ansible units of work. Each task calls a module to do something, such as installing a package, copying a file, creating a user, or starting a service.

Use fully qualified collection names when possible:

```yaml
ansible.builtin.apt
```

## apt

Ubuntu package management:

```yaml
- name: Install nginx
  ansible.builtin.apt:
    name: nginx
    state: present
    update_cache: true
```

## yum

RHEL, CentOS, Amazon Linux 2:

```yaml
- name: Install httpd
  ansible.builtin.yum:
    name: httpd
    state: present
```

## copy

```yaml
- name: Copy index page
  ansible.builtin.copy:
    src: files/index.html
    dest: /var/www/html/index.html
    owner: www-data
    group: www-data
    mode: "0644"
```

## template

Uses Jinja2 templates:

```yaml
- name: Render nginx config
  ansible.builtin.template:
    src: templates/site.conf.j2
    dest: /etc/nginx/sites-available/site.conf
```

## file

```yaml
- name: Create app directory
  ansible.builtin.file:
    path: /opt/app
    state: directory
    owner: deploy
    group: deploy
    mode: "0755"
```

## service

```yaml
- name: Start nginx
  ansible.builtin.service:
    name: nginx
    state: started
    enabled: true
```

## systemd

```yaml
- name: Restart nginx with systemd
  ansible.builtin.systemd:
    name: nginx
    state: restarted
    daemon_reload: true
```

## user

```yaml
- name: Create deploy user
  ansible.builtin.user:
    name: deploy
    shell: /bin/bash
    groups: sudo
    append: true
```

## group

```yaml
- name: Create app group
  ansible.builtin.group:
    name: app
    state: present
```

## git

```yaml
- name: Clone application repository
  ansible.builtin.git:
    repo: https://github.com/example/app.git
    dest: /opt/app
    version: main
```

## lineinfile

```yaml
- name: Disable root SSH login
  ansible.builtin.lineinfile:
    path: /etc/ssh/sshd_config
    regexp: "^PermitRootLogin"
    line: "PermitRootLogin no"
  notify: Restart ssh
```

## blockinfile

```yaml
- name: Add managed block
  ansible.builtin.blockinfile:
    path: /etc/motd
    block: |
      Managed by Ansible
      Contact DevOps team
```

## debug

```yaml
- name: Print OS family
  ansible.builtin.debug:
    msg: "OS family is {{ ansible_facts['os_family'] }}"
```

## setup

Collects facts:

```bash
ansible all -i inventory.ini -m setup
```

Filter:

```bash
ansible all -i inventory.ini -m setup -a "filter=ansible_distribution*"
```

## Mini Practice

- Install nginx with `apt`.
- Create `/opt/demo`.
- Copy a file to `/tmp`.
- Print the managed node OS using `debug`.

