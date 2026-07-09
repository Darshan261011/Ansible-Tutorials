# Ansible Config File

## ansible.cfg

`ansible.cfg` stores project-level Ansible settings.

Recommended project layout:

```text
project/
  ansible.cfg
  inventory.ini
  site.yml
```

Ansible config precedence includes:

1. `ANSIBLE_CONFIG` environment variable.
2. `ansible.cfg` in current directory.
3. `~/.ansible.cfg`.
4. `/etc/ansible/ansible.cfg`.

## Useful Configuration

```ini
[defaults]
inventory = inventory.ini
remote_user = ubuntu
private_key_file = ~/.ssh/id_ed25519
host_key_checking = False
forks = 10
timeout = 30
retry_files_enabled = False

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

## inventory Path

```ini
[defaults]
inventory = inventories/dev.ini
```

Now commands can omit `-i`:

```bash
ansible all -m ping
```

## remote_user

```ini
[defaults]
remote_user = ubuntu
```

This sets the SSH user.

## private_key_file

```ini
[defaults]
private_key_file = ~/.ssh/ansible_lab
```

## host_key_checking

```ini
[defaults]
host_key_checking = False
```

Useful in disposable labs, but production should manage known hosts properly.

## forks

```ini
[defaults]
forks = 20
```

Controls parallelism.

## timeout

```ini
[defaults]
timeout = 30
```

SSH timeout in seconds.

## Privilege Escalation

```ini
[privilege_escalation]
become = True
become_method = sudo
become_user = root
```

## Mini Practice

- Create `ansible.cfg`.
- Set default inventory.
- Run `ansible all -m ping` without `-i`.
- Change `forks` and observe output speed on multiple hosts.

