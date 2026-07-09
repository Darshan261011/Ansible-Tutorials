# YAML For Ansible

## YAML Basics

YAML is a human-readable data format. Ansible playbooks use YAML.

Rules:

- Indentation matters.
- Use spaces, not tabs.
- Key-value pairs use `key: value`.
- Lists use `-`.
- Strings usually do not need quotes.

## Lists

```yaml
packages:
  - nginx
  - git
  - curl
```

Use in loop:

```yaml
- name: Install packages
  ansible.builtin.apt:
    name: "{{ item }}"
    state: present
  loop: "{{ packages }}"
```

## Dictionaries

```yaml
app:
  name: demo
  port: 8080
  user: deploy
```

Access:

```yaml
{{ app.name }}
{{ app.port }}
```

## List Of Dictionaries

```yaml
users:
  - name: deploy
    shell: /bin/bash
  - name: appuser
    shell: /usr/sbin/nologin
```

## Indentation Rules

Correct:

```yaml
- name: Install nginx
  ansible.builtin.apt:
    name: nginx
    state: present
```

Wrong:

```yaml
- name: Install nginx
 ansible.builtin.apt:
    name: nginx
```

The module is not aligned with `name`.

## Booleans

Use clear booleans:

```yaml
become: true
enabled: true
```

## Common YAML Mistakes

| Mistake | Example | Fix |
|---|---|---|
| Tabs | Tab indentation | Use spaces |
| Missing space | `name:nginx` | `name: nginx` |
| Bad alignment | Task keys not aligned | Align keys at same level |
| Unquoted colon | `msg: user: deploy` | `msg: "user: deploy"` |

## Correct Vs Wrong

Correct:

```yaml
vars:
  service_name: nginx
tasks:
  - name: Start service
    ansible.builtin.service:
      name: "{{ service_name }}"
      state: started
```

Wrong:

```yaml
vars:
 service_name: nginx
tasks:
- name: Start service
  ansible.builtin.service:
  name: "{{ service_name }}"
```

## Validate YAML

Use Ansible syntax check:

```bash
ansible-playbook --syntax-check playbook.yml
```

## Mini Practice

- Create a YAML list of packages.
- Create a dictionary for app settings.
- Create a playbook that loops over packages.
- Intentionally break indentation and run syntax check.

