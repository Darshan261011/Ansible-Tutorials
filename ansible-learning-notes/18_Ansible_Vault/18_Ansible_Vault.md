# Ansible Vault

## What Is Ansible Vault?

Ansible Vault encrypts sensitive files so secrets can be stored with playbooks more safely.

Use it for:

- Database passwords.
- API tokens.
- Private app secrets.
- Sudo passwords when unavoidable.

Do not use plain text secrets in normal variable files.

## Encrypting Secrets

Create an encrypted file:

```bash
ansible-vault create group_vars/web/vault.yml
```

Example contents before encryption:

```yaml
vault_db_password: StrongPassword123
vault_api_token: replace-with-real-token
```

Encrypt an existing file:

```bash
ansible-vault encrypt secrets.yml
```

## Decrypting Files

```bash
ansible-vault decrypt secrets.yml
```

View without decrypting permanently:

```bash
ansible-vault view secrets.yml
```

## Editing Encrypted Files

```bash
ansible-vault edit secrets.yml
```

## Using Vault In Playbooks

```yaml
---
- name: Use vault secret
  hosts: web
  vars_files:
    - secrets.yml
  tasks:
    - name: Print masked secret example
      ansible.builtin.debug:
        msg: "Secret variable is loaded"
```

Run:

```bash
ansible-playbook -i inventory.ini site.yml --ask-vault-pass
```

## Password File

Create a password file outside Git:

```bash
echo "my-vault-password" > ~/.ansible-vault-pass
chmod 600 ~/.ansible-vault-pass
```

Run:

```bash
ansible-playbook -i inventory.ini site.yml --vault-password-file ~/.ansible-vault-pass
```

## Best Practices

- Never commit vault password files.
- Encrypt only secret values or secret files.
- Use clear variable names like `vault_db_password`.
- Keep non-secret defaults in normal files.
- Rotate secrets regularly.
- Prefer secret managers in production when available.

## Mini Practice

- Create `secrets.yml` with a test password.
- Encrypt it.
- View it using `ansible-vault view`.
- Use it in a playbook with `--ask-vault-pass`.

