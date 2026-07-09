# Handlers And Notify

## What Are Handlers?

Handlers are special tasks that run only when notified by another task. They are commonly used to restart services after configuration changes.

## Why Handlers Are Used

Without handlers, you may restart a service every time the playbook runs. With handlers, the service restarts only when a task changes something.

## notify

```yaml
- name: Copy nginx config
  ansible.builtin.copy:
    src: nginx.conf
    dest: /etc/nginx/nginx.conf
  notify: Restart nginx
```

Handler:

```yaml
handlers:
  - name: Restart nginx
    ansible.builtin.service:
      name: nginx
      state: restarted
```

The handler name must match the `notify` value.

## Nginx Config Example

```yaml
---
- name: Configure nginx
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true

    - name: Deploy nginx site config
      ansible.builtin.template:
        src: templates/site.conf.j2
        dest: /etc/nginx/sites-available/default
        mode: "0644"
      notify: Restart nginx

    - name: Ensure nginx is running
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

  handlers:
    - name: Restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

## Common Mistakes

| Mistake | Result | Fix |
|---|---|---|
| Handler name mismatch | Handler does not run | Match `notify` and handler `name` |
| No changed task | Handler does not run | This is normal |
| Handler placed under tasks | YAML or logic error | Put under `handlers` |
| Restart every run | Service disruption | Use handler instead of normal restart task |

## Force Handlers

Run handlers even if a later task fails:

```bash
ansible-playbook -i inventory.ini site.yml --force-handlers
```

## Mini Practice

- Create a playbook that copies `/etc/motd`.
- Notify a handler when the file changes.
- Run the playbook twice and compare handler behavior.

