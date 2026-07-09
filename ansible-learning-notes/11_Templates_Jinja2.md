# Templates And Jinja2

## What Is Jinja2?

Jinja2 is a templating language used by Ansible to create files dynamically from variables.

Template files commonly end with `.j2`.

## template Module

```yaml
- name: Render app config
  ansible.builtin.template:
    src: templates/app.conf.j2
    dest: /etc/app/app.conf
    mode: "0644"
```

## Variables Inside Templates

Template:

```jinja
server_name={{ server_name }}
app_port={{ app_port }}
```

Variables:

```yaml
server_name: web.example.com
app_port: 8080
```

## if Condition Inside Template

```jinja
{% if enable_debug %}
debug=true
{% else %}
debug=false
{% endif %}
```

## loop Inside Template

```jinja
{% for backend in backends %}
server {{ backend }};
{% endfor %}
```

Variables:

```yaml
backends:
  - 10.0.1.10:8080
  - 10.0.1.11:8080
```

## Nginx Config Template Example

`templates/site.conf.j2`:

```jinja
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    {% if enable_status %}
    location /status {
        stub_status;
    }
    {% endif %}
}
```

Playbook:

```yaml
---
- name: Render nginx template
  hosts: web
  become: true
  vars:
    nginx_port: 80
    server_name: example.local
    enable_status: true
  tasks:
    - name: Deploy nginx config
      ansible.builtin.template:
        src: templates/site.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

## Mini Practice

- Create an `index.html.j2` template.
- Add hostname using `{{ inventory_hostname }}`.
- Add a loop that prints backend servers.
- Deploy the template to `/var/www/html/index.html`.

