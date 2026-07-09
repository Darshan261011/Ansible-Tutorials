# Common Errors And Fixes

## SSH Permission Denied

Error:

```text
Permission denied (publickey)
```

Common causes:

- Wrong SSH user.
- SSH key not copied.
- Wrong private key.
- Managed node does not allow the user.

Fix:

```bash
ssh-copy-id ubuntu@192.168.56.11
ssh -i ~/.ssh/id_ed25519 ubuntu@192.168.56.11
ansible all -i inventory.ini -m ping -u ubuntu
```

Inventory example:

```ini
web1 ansible_host=192.168.56.11 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

## Host Unreachable

Error:

```text
UNREACHABLE!
```

Fix:

```bash
ping 192.168.56.11
ssh ubuntu@192.168.56.11
ansible all -i inventory.ini --list-hosts
ansible all -i inventory.ini -m ping -vvv
```

Check firewall, DNS, IP address, VPN, and security groups.

## Python Interpreter Issue

Error:

```text
/usr/bin/python: not found
```

Fix on managed node:

```bash
sudo apt update
sudo apt install python3 -y
```

Inventory fix:

```ini
web1 ansible_host=192.168.56.11 ansible_python_interpreter=/usr/bin/python3
```

## YAML Syntax Error

Error:

```text
Syntax Error while loading YAML
```

Fix:

```bash
ansible-playbook --syntax-check site.yml
```

Common causes:

- Tabs instead of spaces.
- Incorrect indentation.
- Missing colon.
- Unquoted string containing colon.

## Sudo Password Issue

Error:

```text
Missing sudo password
```

Fix:

```bash
ansible-playbook -i inventory.ini site.yml --ask-become-pass
```

Or configure passwordless sudo for the automation user when allowed by policy.

## Module Not Found

Error:

```text
couldn't resolve module/action
```

Fix:

```bash
ansible-galaxy collection install community.general
ansible-galaxy collection list
```

Use fully qualified module names:

```yaml
community.general.ufw:
```

## Inventory Not Parsed

Error:

```text
Unable to parse inventory
```

Fix:

```bash
ansible-inventory -i inventory.ini --list
ansible-inventory -i inventory.yml --list
```

Check INI group names, YAML indentation, and file path.

## Handler Not Running

Possible reasons:

- The notifying task did not change.
- Handler name does not match.
- A later task failed before handlers ran.

Fix:

```bash
ansible-playbook -i inventory.ini site.yml --force-handlers
```

Check:

```yaml
notify: Restart nginx
```

matches:

```yaml
- name: Restart nginx
```

## Variable Undefined

Error:

```text
'app_port' is undefined
```

Fix:

- Define the variable in `vars`, `group_vars`, `host_vars`, or `vars_files`.
- Check spelling.
- Print variables with `debug`.

Example:

```yaml
- name: Show variable
  ansible.builtin.debug:
    var: app_port
```

## Quick Troubleshooting Checklist

```bash
ansible --version
ansible-inventory -i inventory.ini --list
ansible all -i inventory.ini -m ping -vvv
ansible-playbook --syntax-check site.yml
ansible-playbook -i inventory.ini site.yml --check --diff
```

