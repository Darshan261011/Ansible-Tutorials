# Ansible Collections

## What Are Collections?

Collections package Ansible content such as modules, roles, plugins, and documentation.

Example module name:

```yaml
ansible.builtin.apt
```

Here `ansible.builtin` is the collection.

## Roles Vs Collections

| Topic | Role | Collection |
|---|---|---|
| Purpose | Reusable automation structure | Package of roles, modules, plugins |
| Contains modules | Usually no | Yes |
| Install command | `ansible-galaxy role install` | `ansible-galaxy collection install` |
| Example | `geerlingguy.nginx` | `community.general` |

## Installing Collections

```bash
ansible-galaxy collection install community.general
```

Install to project path:

```bash
ansible-galaxy collection install community.general -p collections/
```

## Using Collection Modules

```yaml
- name: Use a community module
  community.general.ufw:
    rule: allow
    port: "80"
    proto: tcp
```

## requirements.yml For Collections

```yaml
---
collections:
  - name: community.general
    version: ">=8.0.0"
  - name: ansible.posix
```

Install:

```bash
ansible-galaxy collection install -r requirements.yml
```

## collections Keyword

You can declare collections in a play:

```yaml
---
- name: Example collection usage
  hosts: web
  collections:
    - community.general
  tasks:
    - name: Allow HTTP through UFW
      ufw:
        rule: allow
        port: "80"
        proto: tcp
```

Using fully qualified names is clearer:

```yaml
community.general.ufw:
```

## Mini Practice

- Install `community.general`.
- Create collection `requirements.yml`.
- Use a fully qualified collection module in a playbook.
- Compare role and collection installation commands.

