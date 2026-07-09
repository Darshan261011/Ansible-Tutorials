# Conditionals And Loops

## when Condition

`when` runs a task only if a condition is true.

```yaml
- name: Install nginx only on Ubuntu
  ansible.builtin.apt:
    name: nginx
    state: present
  when: ansible_facts['distribution'] == "Ubuntu"
```

## Facts-Based Conditions

Install package based on OS family:

```yaml
- name: Install nginx on Debian family
  ansible.builtin.apt:
    name: nginx
    state: present
    update_cache: true
  when: ansible_facts['os_family'] == "Debian"

- name: Install httpd on RedHat family
  ansible.builtin.yum:
    name: httpd
    state: present
  when: ansible_facts['os_family'] == "RedHat"
```

## Loops

Install multiple packages:

```yaml
- name: Install packages
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - git
    - curl
```

## with_items

Older style:

```yaml
- name: Create users
  ansible.builtin.user:
    name: "{{ item }}"
    state: present
  with_items:
    - deploy
    - appuser
```

Prefer `loop` in new playbooks.

## Loop With Dictionaries

```yaml
- name: Create users with shells
  ansible.builtin.user:
    name: "{{ item.name }}"
    shell: "{{ item.shell }}"
    state: present
  loop:
    - name: deploy
      shell: /bin/bash
    - name: appuser
      shell: /usr/sbin/nologin
```

## failed_when

Control when a task is considered failed.

```yaml
- name: Check app health
  ansible.builtin.command: curl -s http://localhost:8080/health
  register: health
  changed_when: false
  failed_when: "'OK' not in health.stdout"
```

## changed_when

Control whether a task reports changed.

```yaml
- name: Check uptime without reporting change
  ansible.builtin.command: uptime
  register: uptime_result
  changed_when: false
```

## Real Example

```yaml
---
- name: Install web package by OS
  hosts: web
  become: true
  tasks:
    - name: Install Ubuntu packages
      ansible.builtin.apt:
        name: "{{ item }}"
        state: present
        update_cache: true
      loop:
        - nginx
        - git
      when: ansible_facts['os_family'] == "Debian"

    - name: Print installed packages
      ansible.builtin.debug:
        msg: "Installed {{ item }} on {{ inventory_hostname }}"
      loop:
        - nginx
        - git
```

## Mini Practice

- Install `nginx` only on Ubuntu.
- Create two users using a loop.
- Run `uptime` without reporting changed.
- Fail a task when command output does not contain expected text.

