# Variables In Ansible

## What Are Variables?

Variables store values that can change between environments, hosts, or runs.

Example:

```yaml
package_name: nginx
service_name: nginx
```

Use:

```yaml
name: "{{ package_name }}"
```

## Playbook Variables

```yaml
---
- name: Use play variables
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

## Inventory Variables

```ini
web1 ansible_host=192.168.56.11 app_port=8080
```

## group_vars

Directory:

```text
group_vars/
  web.yml
```

`group_vars/web.yml`:

```yaml
package_name: nginx
service_name: nginx
app_port: 8080
```

## host_vars

Directory:

```text
host_vars/
  web1.yml
```

`host_vars/web1.yml`:

```yaml
app_port: 8081
```

Host variables override group variables for that host.

## vars_files

```yaml
- name: Load variables from file
  hosts: web
  vars_files:
    - vars/app.yml
```

`vars/app.yml`:

```yaml
app_name: demo-app
app_user: deploy
```

## Extra Vars

Passed at runtime:

```bash
ansible-playbook -i inventory.ini site.yml -e "app_version=1.2.0"
```

Extra vars have very high precedence. Use them carefully.

## Register Variables

Capture task output:

```yaml
- name: Check nginx status
  ansible.builtin.command: systemctl is-active nginx
  register: nginx_status
  changed_when: false

- name: Print status
  ansible.builtin.debug:
    var: nginx_status.stdout
```

## Facts

Facts are variables collected from managed nodes.

```yaml
- name: Print distribution
  ansible.builtin.debug:
    msg: "{{ ansible_facts['distribution'] }}"
```

## Variable Precedence In Simple Language

If the same variable is defined in multiple places, Ansible chooses the most specific or highest-priority value.

Simplified order from lower to higher:

1. Role defaults.
2. Group variables.
3. Host variables.
4. Play variables.
5. Vars files.
6. Extra vars from `-e`.

## Mini Practice

- Define `package_name` in `group_vars/web.yml`.
- Override it for one host in `host_vars/web1.yml`.
- Register command output and print it.
- Run a playbook with `-e`.

