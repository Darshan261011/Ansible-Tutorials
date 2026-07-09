# Tags And Check Mode

## What Are Tags?

Tags let you run selected tasks from a playbook.

```yaml
- name: Install nginx
  ansible.builtin.apt:
    name: nginx
    state: present
  tags:
    - packages
    - nginx
```

Run only tagged tasks:

```bash
ansible-playbook -i inventory.ini site.yml --tags packages
```

## Skipping Tags

```bash
ansible-playbook -i inventory.ini site.yml --skip-tags firewall
```

## Multiple Tags

```bash
ansible-playbook -i inventory.ini site.yml --tags "packages,config"
```

## Always Tag

```yaml
- name: Always print deployment start
  ansible.builtin.debug:
    msg: "Starting deployment"
  tags:
    - always
```

## Check Mode

Check mode shows what would change without applying changes.

```bash
ansible-playbook -i inventory.ini site.yml --check
```

Not every module fully supports check mode, especially command-like tasks.

## Diff Mode

Shows text file differences:

```bash
ansible-playbook -i inventory.ini site.yml --diff
```

Use both:

```bash
ansible-playbook -i inventory.ini site.yml --check --diff
```

## Practical Example

```yaml
---
- name: Tagged nginx setup
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true
      tags: packages

    - name: Copy index page
      ansible.builtin.copy:
        content: "Hello from Ansible\n"
        dest: /var/www/html/index.html
      tags: content

    - name: Start nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
      tags: service
```

## Mini Practice

- Add tags to package, config, and service tasks.
- Run only config tasks.
- Skip service tasks.
- Run the playbook with `--check --diff`.

