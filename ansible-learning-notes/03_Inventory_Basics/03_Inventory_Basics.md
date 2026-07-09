# Inventory Basics

## What Is Inventory?

Inventory is the list of servers Ansible manages. It tells Ansible hostnames, IP addresses, users, groups, and connection settings.

Default inventory path is often:

```bash
/etc/ansible/hosts
```

For projects, keep inventory inside the project folder.

## Static Inventory

A static inventory is a file you manually maintain.

Run with:

```bash
ansible all -i inventory.ini -m ping
```

## INI Inventory

```ini
[web]
web1 ansible_host=192.168.56.11 ansible_user=ubuntu
web2 ansible_host=192.168.56.12 ansible_user=ubuntu

[db]
db1 ansible_host=192.168.56.21 ansible_user=ubuntu
```

## YAML Inventory

```yaml
all:
  children:
    web:
      hosts:
        web1:
          ansible_host: 192.168.56.11
          ansible_user: ubuntu
        web2:
          ansible_host: 192.168.56.12
          ansible_user: ubuntu
```

Run:

```bash
ansible all -i inventory.yml -m ping
```

## Groups And Child Groups

```ini
[frontend]
web1
web2

[backend]
api1
api2

[production:children]
frontend
backend
```

Run a command for the parent group:

```bash
ansible production -i inventory.ini -m ping
```

## Host Variables

Host variables apply to one host.

```ini
web1 ansible_host=192.168.56.11 http_port=80
web2 ansible_host=192.168.56.12 http_port=8080
```

Use in playbook:

```yaml
- debug:
    msg: "HTTP port is {{ http_port }}"
```

## Group Variables

Group variables apply to all hosts in a group.

```ini
[web]
web1
web2

[web:vars]
ansible_user=ubuntu
package_name=nginx
```

Better project style:

```text
group_vars/
  web.yml
```

```yaml
package_name: nginx
service_name: nginx
```

## Testing Inventory

List hosts:

```bash
ansible all -i inventory.ini --list-hosts
```

Show parsed inventory:

```bash
ansible-inventory -i inventory.ini --list
```

Ping:

```bash
ansible web -i inventory.ini -m ping
```

## Mini Practice

- Create INI and YAML inventory files.
- Add `web` and `db` groups.
- Run `ansible-inventory --list`.
- Ping only the `web` group.

