# Ansible Galaxy

## What Is Ansible Galaxy?

Ansible Galaxy is a place to find, install, create, and share Ansible roles and collections.

Command:

```bash
ansible-galaxy --help
```

## Installing Roles From Galaxy

```bash
ansible-galaxy role install geerlingguy.nginx
```

Install to a project folder:

```bash
ansible-galaxy role install geerlingguy.nginx -p roles/
```

Use installed role:

```yaml
---
- name: Use Galaxy nginx role
  hosts: web
  become: true
  roles:
    - geerlingguy.nginx
```

## requirements.yml For Roles

```yaml
---
roles:
  - name: geerlingguy.nginx
    version: 3.2.0
  - name: geerlingguy.firewall
```

Install:

```bash
ansible-galaxy role install -r requirements.yml -p roles/
```

## Installing Multiple Roles

Add all roles to `requirements.yml`, commit the file, and let each user install dependencies:

```bash
ansible-galaxy install -r requirements.yml
```

## Creating Your Own Role

```bash
ansible-galaxy role init mycompany.nginx
```

Important files:

- `README.md`: explains usage.
- `defaults/main.yml`: user-configurable variables.
- `tasks/main.yml`: role tasks.
- `meta/main.yml`: role metadata and dependencies.

## Sharing Roles

To share a role:

1. Store it in a Git repository.
2. Write a useful README.
3. Add defaults for configurable values.
4. Avoid hard-coded environment-specific values.
5. Tag releases.

## Best Practices

- Pin role versions for production.
- Read role source before using it.
- Prefer maintained roles.
- Do not store secrets in Galaxy roles.
- Keep role variables documented.

## Mini Practice

- Create `requirements.yml`.
- Install one public role into `roles/`.
- Create your own local role.
- Add a README explaining role variables.

