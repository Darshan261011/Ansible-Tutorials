# Ansible Playbooks Basics

## What Is A Playbook?

A playbook is a YAML file containing automation tasks. It is used when work is repeatable or has multiple steps.

Example:

```yaml
---
- name: Install nginx
  hosts: web
  become: true
  tasks:
    - name: Install nginx package
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true
```

## YAML Structure

Important parts:

- `---` starts the YAML document.
- A playbook contains one or more plays.
- Each play targets hosts.
- Each play contains tasks.
- Each task calls a module.

## hosts

`hosts` selects inventory hosts or groups.

```yaml
hosts: web
```

## become

`become: true` runs tasks with sudo.

```yaml
become: true
```

## tasks

Tasks are executed in order.

```yaml
tasks:
  - name: Install nginx
    ansible.builtin.apt:
      name: nginx
      state: present
```

## name

Use clear task names because they appear in Ansible output.

Good:

```yaml
- name: Ensure nginx is installed
```

Bad:

```yaml
- name: Task 1
```

## Running Playbooks

```bash
ansible-playbook -i inventory.ini install-nginx.yml
```

## Dry Run / Check Mode

Check what would change:

```bash
ansible-playbook -i inventory.ini install-nginx.yml --check
```

Show file differences:

```bash
ansible-playbook -i inventory.ini install-nginx.yml --diff
```

## Syntax Check

```bash
ansible-playbook --syntax-check install-nginx.yml
```

## First Playbook: Install Nginx

```yaml
---
- name: Configure web servers
  hosts: web
  become: true
  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: true

    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Start and enable nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

Run:

```bash
ansible-playbook -i inventory.ini install-nginx.yml
```

## Mini Practice

- Create `install-tools.yml`.
- Install `git`, `curl`, and `unzip`.
- Run syntax check.
- Run in check mode.
- Run normally.

