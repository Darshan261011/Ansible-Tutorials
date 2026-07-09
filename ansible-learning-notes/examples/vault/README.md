# Vault Example

Create an encrypted variable file:

```bash
ansible-vault create secrets.yml
```

Example variables to place inside:

```yaml
vault_app_secret: sample-secret-value
vault_db_password: sample-db-password
```

Run a playbook that uses it:

```bash
ansible-playbook -i ../inventory/inventory.ini use-vault.yml --ask-vault-pass
```

Do not commit vault password files.

