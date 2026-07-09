# 20 Practical Ansible Playbook Examples

This file contains 20 playbook examples that cover the main Ansible concepts from the learning notes: inventory usage, ad-hoc style tasks, modules, variables, facts, conditionals, loops, handlers, templates, tags, check mode, error handling, roles, Galaxy, collections, Vault, dynamic inventory concepts, `ansible.cfg`, and advanced playbook patterns.

Use these examples with a sample inventory:

```ini
[web]
web1 ansible_host=192.168.56.11 ansible_user=ubuntu
web2 ansible_host=192.168.56.12 ansible_user=ubuntu

[db]
db1 ansible_host=192.168.56.21 ansible_user=ubuntu
```

Run a playbook:

```bash
ansible-playbook -i inventory.ini playbook-name.yml
```

## 1. Connectivity Test With Ping Module

Concepts covered: inventory, `ping` module, basic playbook structure.

```yaml
---
- name: Test Ansible connectivity
  hosts: all
  gather_facts: false
  tasks:
    - name: Ping all managed nodes
      ansible.builtin.ping:
```

## 2. Install Common Packages On Ubuntu

Concepts covered: `apt` module, `become`, package installation.

```yaml
---
- name: Install common packages
  hosts: all
  become: true
  tasks:
    - name: Install git, curl, unzip, and tree
      ansible.builtin.apt:
        name:
          - git
          - curl
          - unzip
          - tree
        state: present
        update_cache: true
```

## 3. Create A Linux User

Concepts covered: `user` module, sudo access, managed user creation.

```yaml
---
- name: Create deployment user
  hosts: all
  become: true
  tasks:
    - name: Ensure deploy user exists
      ansible.builtin.user:
        name: deploy
        shell: /bin/bash
        groups: sudo
        append: true
        create_home: true
```

## 4. Create Directories And Copy Files

Concepts covered: `file` module, `copy` module, ownership, permissions.

```yaml
---
- name: Create application directory and copy file
  hosts: web
  become: true
  tasks:
    - name: Create application directory
      ansible.builtin.file:
        path: /opt/demo-app
        state: directory
        owner: deploy
        group: deploy
        mode: "0755"

    - name: Copy application info file
      ansible.builtin.copy:
        content: "Demo application managed by Ansible\n"
        dest: /opt/demo-app/app.txt
        owner: deploy
        group: deploy
        mode: "0644"
```

## 5. Install And Start Nginx

Concepts covered: `apt`, `service`, web server setup.

```yaml
---
- name: Install and start nginx
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true

    - name: Start and enable nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

## 6. Use Variables In A Playbook

Concepts covered: play variables, variable interpolation.

```yaml
---
- name: Install package using variables
  hosts: web
  become: true
  vars:
    package_name: nginx
    service_name: nginx
    web_root: /var/www/html
  tasks:
    - name: Install web package
      ansible.builtin.apt:
        name: "{{ package_name }}"
        state: present
        update_cache: true

    - name: Create web root
      ansible.builtin.file:
        path: "{{ web_root }}"
        state: directory
        mode: "0755"

    - name: Start web service
      ansible.builtin.service:
        name: "{{ service_name }}"
        state: started
        enabled: true
```

## 7. Use Loops To Install Packages

Concepts covered: loops, list variables, package automation.

```yaml
---
- name: Install multiple packages with loop
  hosts: all
  become: true
  vars:
    packages:
      - git
      - curl
      - htop
      - net-tools
  tasks:
    - name: Install each package
      ansible.builtin.apt:
        name: "{{ item }}"
        state: present
        update_cache: true
      loop: "{{ packages }}"
```

## 8. Create Multiple Users With Loop Dictionaries

Concepts covered: loop with dictionaries, users, shells.

```yaml
---
- name: Create multiple users
  hosts: all
  become: true
  vars:
    app_users:
      - name: deploy
        shell: /bin/bash
        group: sudo
      - name: appuser
        shell: /usr/sbin/nologin
        group: users
  tasks:
    - name: Ensure users exist
      ansible.builtin.user:
        name: "{{ item.name }}"
        shell: "{{ item.shell }}"
        groups: "{{ item.group }}"
        append: true
        state: present
      loop: "{{ app_users }}"
```

## 9. Conditional Package Install Based On OS Family

Concepts covered: facts, `when`, `apt`, `yum`.

```yaml
---
- name: Install web server based on OS family
  hosts: web
  become: true
  tasks:
    - name: Install nginx on Debian or Ubuntu
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

## 10. Register Output And Debug It

Concepts covered: `register`, `debug`, `changed_when`.

```yaml
---
- name: Register and print command output
  hosts: all
  tasks:
    - name: Check system uptime
      ansible.builtin.command: uptime
      register: uptime_result
      changed_when: false

    - name: Print uptime output
      ansible.builtin.debug:
        msg: "{{ inventory_hostname }} uptime is {{ uptime_result.stdout }}"
```

## 11. Handler To Restart Nginx Only On Change

Concepts covered: handlers, `notify`, service restart only when needed.

```yaml
---
- name: Restart nginx only when index changes
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true

    - name: Update index page
      ansible.builtin.copy:
        content: "Nginx managed by Ansible on {{ inventory_hostname }}\n"
        dest: /var/www/html/index.html
        mode: "0644"
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

## 12. Deploy Nginx Config With Jinja2 Template

Concepts covered: templates, Jinja2 variables, handler.

Create `templates/nginx-site.conf.j2`:

```jinja
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};
    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Playbook:

```yaml
---
- name: Deploy nginx template
  hosts: web
  become: true
  vars:
    nginx_port: 80
    server_name: "_"
  tasks:
    - name: Deploy default nginx site
      ansible.builtin.template:
        src: templates/nginx-site.conf.j2
        dest: /etc/nginx/sites-available/default
        mode: "0644"
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

## 13. Use Ansible Facts In A Template

Concepts covered: facts, templates, inventory hostname.

Create `templates/index.html.j2`:

```jinja
<!doctype html>
<html>
<body>
  <h1>{{ app_name }}</h1>
  <p>Host: {{ inventory_hostname }}</p>
  <p>OS: {{ ansible_facts['distribution'] }} {{ ansible_facts['distribution_version'] }}</p>
  <p>IP: {{ ansible_facts['default_ipv4']['address'] | default('unknown') }}</p>
</body>
</html>
```

Playbook:

```yaml
---
- name: Create fact-based index page
  hosts: web
  become: true
  vars:
    app_name: Fact Demo App
  tasks:
    - name: Render index page
      ansible.builtin.template:
        src: templates/index.html.j2
        dest: /var/www/html/index.html
        mode: "0644"
```

## 14. Tags For Selective Execution

Concepts covered: tags, selective task runs.

Run only package tasks:

```bash
ansible-playbook -i inventory.ini tagged-web.yml --tags packages
```

Skip service tasks:

```bash
ansible-playbook -i inventory.ini tagged-web.yml --skip-tags service
```

Playbook:

```yaml
---
- name: Tagged web server setup
  hosts: web
  become: true
  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true
      tags:
        - packages

    - name: Copy index page
      ansible.builtin.copy:
        content: "Tagged deployment example\n"
        dest: /var/www/html/index.html
      tags:
        - content

    - name: Ensure nginx is running
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
      tags:
        - service
```

## 15. Check Mode And Diff Mode Friendly Playbook

Concepts covered: check mode, diff mode, file changes.

Run:

```bash
ansible-playbook -i inventory.ini motd.yml --check --diff
```

Playbook:

```yaml
---
- name: Manage message of the day
  hosts: all
  become: true
  tasks:
    - name: Update MOTD
      ansible.builtin.copy:
        content: |
          This server is managed by Ansible.
          Host: {{ inventory_hostname }}
        dest: /etc/motd
        owner: root
        group: root
        mode: "0644"
```

## 16. Error Handling With block, rescue, always

Concepts covered: `block`, `rescue`, `always`, debugging failures.

```yaml
---
- name: Demonstrate error handling
  hosts: web
  become: true
  tasks:
    - name: Deploy with recovery
      block:
        - name: Copy application release
          ansible.builtin.copy:
            content: "release=1.0.0\n"
            dest: /opt/demo-app/release.txt
            mode: "0644"

        - name: Validate nginx config
          ansible.builtin.command: nginx -t
          changed_when: false

      rescue:
        - name: Print recovery message
          ansible.builtin.debug:
            msg: "Deployment failed on {{ inventory_hostname }}. Check nginx config and paths."

      always:
        - name: Always print final status
          ansible.builtin.debug:
            msg: "Deployment attempt completed for {{ inventory_hostname }}"
```

## 17. Custom Failure And Change Conditions

Concepts covered: `failed_when`, `changed_when`, health checks.

```yaml
---
- name: Health check web service
  hosts: web
  tasks:
    - name: Check nginx HTTP response
      ansible.builtin.command: curl -s -o /tmp/health.out -w "%{http_code}" http://localhost
      register: health_check
      changed_when: false
      failed_when: health_check.stdout != "200"

    - name: Print health status
      ansible.builtin.debug:
        msg: "HTTP status from {{ inventory_hostname }} is {{ health_check.stdout }}"
```

## 18. Use Ansible Vault Secrets

Concepts covered: Vault, `vars_files`, secret variables.

Create encrypted file:

```bash
ansible-vault create secrets.yml
```

Example content inside the encrypted file:

```yaml
vault_app_secret: super-secret-value
```

Run:

```bash
ansible-playbook -i inventory.ini vault-example.yml --ask-vault-pass
```

Playbook:

```yaml
---
- name: Use vault secret safely
  hosts: web
  vars_files:
    - secrets.yml
  tasks:
    - name: Confirm secret is loaded without printing it
      ansible.builtin.debug:
        msg: "Vault secret is loaded: {{ vault_app_secret is defined }}"
```

## 19. Role-Based Web Server Playbook

Concepts covered: roles, defaults, tasks, handlers, templates.

Expected structure:

```text
roles/
  webserver/
    defaults/main.yml
    tasks/main.yml
    handlers/main.yml
    templates/index.html.j2
```

`roles/webserver/tasks/main.yml`:

```yaml
---
- name: Install nginx
  ansible.builtin.apt:
    name: "{{ nginx_package }}"
    state: present
    update_cache: true

- name: Render index page
  ansible.builtin.template:
    src: index.html.j2
    dest: /var/www/html/index.html
    mode: "0644"
  notify: Restart nginx

- name: Start nginx
  ansible.builtin.service:
    name: "{{ nginx_service }}"
    state: started
    enabled: true
```

`roles/webserver/defaults/main.yml`:

```yaml
---
nginx_package: nginx
nginx_service: nginx
app_name: Role Demo App
```

`roles/webserver/handlers/main.yml`:

```yaml
---
- name: Restart nginx
  ansible.builtin.service:
    name: nginx
    state: restarted
```

Playbook:

```yaml
---
- name: Configure web servers using role
  hosts: web
  become: true
  roles:
    - webserver
```

## 20. Advanced Rolling Deployment

Concepts covered: `serial`, `delegate_to`, `run_once`, registered output, handlers, rolling update pattern.

```yaml
---
- name: Rolling deployment to web servers
  hosts: web
  become: true
  serial: 1
  vars:
    app_version: "2.0.0"
  tasks:
    - name: Announce deployment once
      ansible.builtin.debug:
        msg: "Starting rolling deployment of version {{ app_version }}"
      run_once: true

    - name: Simulate removing host from load balancer
      ansible.builtin.debug:
        msg: "Remove {{ inventory_hostname }} from load balancer"
      delegate_to: localhost

    - name: Deploy application version page
      ansible.builtin.copy:
        content: "Application version {{ app_version }} on {{ inventory_hostname }}\n"
        dest: /var/www/html/index.html
        mode: "0644"
      notify: Restart nginx

    - name: Validate nginx configuration
      ansible.builtin.command: nginx -t
      register: nginx_config_test
      changed_when: false
      failed_when: nginx_config_test.rc != 0

    - name: Simulate adding host back to load balancer
      ansible.builtin.debug:
        msg: "Add {{ inventory_hostname }} back to load balancer"
      delegate_to: localhost

  handlers:
    - name: Restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

## Bonus: Galaxy And Collections Requirements

Some playbooks need external collections, such as `community.general.ufw`.

`requirements.yml`:

```yaml
---
collections:
  - name: community.general

roles:
  - name: geerlingguy.nginx
```

Install:

```bash
ansible-galaxy collection install -r requirements.yml
ansible-galaxy role install -r requirements.yml
```

## Bonus: ansible.cfg For These Examples

```ini
[defaults]
inventory = inventory.ini
remote_user = ubuntu
host_key_checking = False
retry_files_enabled = False
timeout = 30

[privilege_escalation]
become = True
become_method = sudo
become_user = root
```

