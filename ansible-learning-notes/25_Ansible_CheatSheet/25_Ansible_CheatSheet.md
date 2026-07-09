# Ansible CheatSheet

## Important Commands

```bash
ansible --version
ansible all -i inventory.ini -m ping
ansible all -i inventory.ini --list-hosts
ansible-inventory -i inventory.ini --list
ansible-inventory -i inventory.ini --graph
ansible-playbook -i inventory.ini site.yml
ansible-playbook --syntax-check site.yml
ansible-playbook -i inventory.ini site.yml --check --diff
```

## Inventory Examples

INI:

```ini
[web]
web1 ansible_host=192.168.56.11 ansible_user=ubuntu
web2 ansible_host=192.168.56.12 ansible_user=ubuntu

[web:vars]
ansible_python_interpreter=/usr/bin/python3
```

YAML:

```yaml
all:
  children:
    web:
      hosts:
        web1:
          ansible_host: 192.168.56.11
          ansible_user: ubuntu
```

## Playbook Syntax

```yaml
---
- name: Example play
  hosts: web
  become: true
  vars:
    package_name: nginx
  tasks:
    - name: Install package
      ansible.builtin.apt:
        name: "{{ package_name }}"
        state: present
```

## Module Examples

```yaml
- ansible.builtin.apt:
    name: nginx
    state: present

- ansible.builtin.copy:
    src: index.html
    dest: /var/www/html/index.html

- ansible.builtin.template:
    src: site.conf.j2
    dest: /etc/nginx/sites-available/default

- ansible.builtin.service:
    name: nginx
    state: restarted

- ansible.builtin.user:
    name: deploy
    shell: /bin/bash
```

## Galaxy Commands

```bash
ansible-galaxy role init roles/nginx
ansible-galaxy role install geerlingguy.nginx -p roles/
ansible-galaxy role install -r requirements.yml
ansible-galaxy collection install community.general
ansible-galaxy collection install -r requirements.yml
ansible-galaxy collection list
```

## Vault Commands

```bash
ansible-vault create secrets.yml
ansible-vault encrypt secrets.yml
ansible-vault view secrets.yml
ansible-vault edit secrets.yml
ansible-vault decrypt secrets.yml
ansible-playbook site.yml --ask-vault-pass
ansible-playbook site.yml --vault-password-file ~/.ansible-vault-pass
```

## Role Commands

```bash
ansible-galaxy role init roles/webserver
ansible-playbook -i inventory.ini site.yml
```

Role layout:

```text
roles/webserver/
  tasks/main.yml
  handlers/main.yml
  templates/
  files/
  defaults/main.yml
  vars/main.yml
  meta/main.yml
```

## Conditionals And Loops

```yaml
- name: Install only on Ubuntu
  ansible.builtin.apt:
    name: nginx
    state: present
  when: ansible_facts['distribution'] == "Ubuntu"

- name: Install packages
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - git
```

## Handler Pattern

```yaml
tasks:
  - name: Deploy config
    ansible.builtin.template:
      src: app.conf.j2
      dest: /etc/app.conf
    notify: Restart app

handlers:
  - name: Restart app
    ansible.builtin.service:
      name: app
      state: restarted
```

## Troubleshooting Commands

```bash
ssh ubuntu@192.168.56.11
ansible all -i inventory.ini -m ping -vvv
ansible-playbook -i inventory.ini site.yml -vvv
ansible-playbook --syntax-check site.yml
ansible-config dump --only-changed
ansible-doc ansible.builtin.apt
```

