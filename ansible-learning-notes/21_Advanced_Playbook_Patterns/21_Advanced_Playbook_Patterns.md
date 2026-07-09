# Advanced Playbook Patterns

## include_tasks

Dynamically includes tasks during playbook execution.

```yaml
- name: Include Ubuntu tasks
  ansible.builtin.include_tasks: ubuntu.yml
  when: ansible_facts['distribution'] == "Ubuntu"
```

## import_tasks

Statically imports tasks when the playbook is parsed.

```yaml
- name: Import common tasks
  ansible.builtin.import_tasks: common.yml
```

Use `import_tasks` when the task list is always needed. Use `include_tasks` when conditions or loops decide what to include.

## include_vars

```yaml
- name: Load environment variables
  ansible.builtin.include_vars:
    file: "vars/{{ env }}.yml"
```

Run:

```bash
ansible-playbook -i inventory.ini site.yml -e "env=dev"
```

## import_playbook

```yaml
---
- import_playbook: base.yml
- import_playbook: web.yml
- import_playbook: monitoring.yml
```

## Serial Execution

Use `serial` for rolling updates.

```yaml
- name: Rolling nginx update
  hosts: web
  become: true
  serial: 1
  tasks:
    - name: Update index page
      ansible.builtin.template:
        src: index.html.j2
        dest: /var/www/html/index.html
```

This updates one host at a time.

## Delegation

Run a task on another host.

```yaml
- name: Add host to load balancer
  ansible.builtin.command: /usr/local/bin/add_backend {{ inventory_hostname }}
  delegate_to: loadbalancer1
```

## run_once

Run a task only once, even when many hosts are targeted.

```yaml
- name: Run database migration once
  ansible.builtin.command: /opt/app/migrate.sh
  run_once: true
```

## local_action

Run a task on the control node.

```yaml
- name: Create local deployment log
  local_action:
    module: ansible.builtin.copy
    content: "Deployment started\n"
    dest: ./deploy.log
```

Equivalent:

```yaml
- name: Create local deployment log
  ansible.builtin.copy:
    content: "Deployment started\n"
    dest: ./deploy.log
  delegate_to: localhost
```

## Practical Example

```yaml
---
- name: Rolling web deployment
  hosts: web
  become: true
  serial: 1
  vars:
    app_version: "1.0.0"
  tasks:
    - name: Remove server from load balancer
      ansible.builtin.debug:
        msg: "Remove {{ inventory_hostname }} from load balancer"
      delegate_to: localhost

    - name: Deploy app version
      ansible.builtin.copy:
        content: "Version {{ app_version }} on {{ inventory_hostname }}\n"
        dest: /var/www/html/index.html

    - name: Check nginx
      ansible.builtin.command: systemctl is-active nginx
      register: nginx_status
      changed_when: false
      failed_when: nginx_status.stdout != "active"

    - name: Add server back to load balancer
      ansible.builtin.debug:
        msg: "Add {{ inventory_hostname }} back to load balancer"
      delegate_to: localhost
```

## Mini Practice

- Split tasks into `install.yml` and `config.yml`.
- Import both from `site.yml`.
- Run a rolling update with `serial: 1`.
- Use `delegate_to: localhost` for a local log task.

