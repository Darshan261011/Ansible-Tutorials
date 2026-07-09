# Error Handling

## ignore_errors

Continue even if a task fails:

```yaml
- name: Try to stop a service that may not exist
  ansible.builtin.service:
    name: old-service
    state: stopped
  ignore_errors: true
```

Use carefully because it can hide real problems.

## failed_when

Define custom failure logic:

```yaml
- name: Check app health
  ansible.builtin.command: curl -s http://localhost/health
  register: health
  changed_when: false
  failed_when: "'healthy' not in health.stdout"
```

## changed_when

Avoid false changed results:

```yaml
- name: Read service status
  ansible.builtin.command: systemctl is-active nginx
  register: nginx_status
  changed_when: false
```

## block, rescue, always

```yaml
- name: Deploy with recovery
  block:
    - name: Copy app file
      ansible.builtin.copy:
        src: app.tar.gz
        dest: /opt/app/app.tar.gz

    - name: Restart app
      ansible.builtin.service:
        name: app
        state: restarted

  rescue:
    - name: Show failure message
      ansible.builtin.debug:
        msg: "Deployment failed, running rescue tasks"

  always:
    - name: Always print completion
      ansible.builtin.debug:
        msg: "Deployment attempt finished"
```

## Debugging Failed Playbooks

Useful flags:

```bash
ansible-playbook -i inventory.ini site.yml -v
ansible-playbook -i inventory.ini site.yml -vvv
ansible-playbook -i inventory.ini site.yml --step
ansible-playbook -i inventory.ini site.yml --start-at-task "Install nginx"
```

## Common Troubleshooting Tips

- Check inventory parsing with `ansible-inventory --list`.
- Test SSH manually.
- Run `ansible all -m ping`.
- Use `debug` for variables.
- Use `--syntax-check` before running.
- Use `--check --diff` for configuration files.

## Mini Practice

- Create a task that fails intentionally.
- Add `ignore_errors`.
- Replace it with `failed_when`.
- Wrap tasks in `block`, `rescue`, and `always`.

