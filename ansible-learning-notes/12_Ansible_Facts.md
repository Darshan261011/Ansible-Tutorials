# Ansible Facts

## What Are Facts?

Facts are system details collected from managed nodes. Examples include OS, IP addresses, memory, CPU, hostname, and disks.

Ansible gathers facts automatically at the start of a play unless disabled.

## setup Module

Collect all facts:

```bash
ansible all -i inventory.ini -m setup
```

Filter facts:

```bash
ansible all -i inventory.ini -m setup -a "filter=ansible_distribution*"
```

## Useful Facts

| Fact | Meaning |
|---|---|
| `ansible_facts['distribution']` | Ubuntu, Debian, Amazon |
| `ansible_facts['os_family']` | Debian, RedHat |
| `ansible_facts['hostname']` | Hostname |
| `ansible_facts['default_ipv4']['address']` | Main IPv4 address |
| `ansible_facts['processor_vcpus']` | CPU count |
| `ansible_facts['memtotal_mb']` | Memory in MB |

## Using Facts In Playbooks

```yaml
- name: Print server details
  hosts: all
  tasks:
    - name: Show OS
      ansible.builtin.debug:
        msg: "{{ inventory_hostname }} runs {{ ansible_facts['distribution'] }}"
```

## OS Family Example

```yaml
- name: Install package by OS family
  hosts: all
  become: true
  tasks:
    - name: Install nginx on Debian
      ansible.builtin.apt:
        name: nginx
        state: present
      when: ansible_facts['os_family'] == "Debian"

    - name: Install httpd on RedHat
      ansible.builtin.yum:
        name: httpd
        state: present
      when: ansible_facts['os_family'] == "RedHat"
```

## Disabling Facts

Disable fact gathering for faster playbooks when facts are not needed:

```yaml
- name: Fast play
  hosts: all
  gather_facts: false
  tasks:
    - name: Ping
      ansible.builtin.ping:
```

## Mini Practice

- Print each host's distribution.
- Print each host's default IP.
- Write a condition based on OS family.
- Disable facts and observe what happens if you use `ansible_facts`.

