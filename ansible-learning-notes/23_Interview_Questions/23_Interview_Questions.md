# Ansible Interview Questions

## Beginner

### 1. What is Ansible?

Ansible is an automation tool used for configuration management, application deployment, and operational tasks.

### 2. Is Ansible agentless?

Yes. For Linux systems it usually connects over SSH and does not need an agent service on managed nodes.

### 3. What is a control node?

The control node is the machine where Ansible is installed and commands are executed.

### 4. What is a managed node?

A managed node is a server controlled by Ansible.

### 5. What is inventory?

Inventory is the list of hosts and groups managed by Ansible.

### 6. What is an ad-hoc command?

An ad-hoc command is a one-line Ansible command used for quick tasks.

### 7. What is a playbook?

A playbook is a YAML file containing plays and tasks.

### 8. What is `become`?

`become` allows Ansible to run tasks with privilege escalation, usually sudo.

## Intermediate

### 9. What is idempotency?

Idempotency means running the same task many times results in the same final state without unnecessary changes.

### 10. What are modules?

Modules are units of work used by tasks, such as `apt`, `copy`, `service`, and `template`.

### 11. Difference between `command` and `shell`?

`command` runs commands without shell features. `shell` supports pipes, redirects, variables, and other shell behavior.

### 12. What are handlers?

Handlers are tasks that run only when notified by changed tasks. They are often used to restart services.

### 13. What is a Jinja2 template?

A template is a dynamic file that uses variables, loops, and conditions before being copied to a managed node.

### 14. What are facts?

Facts are system details collected from managed nodes, such as OS, memory, IP, and CPU.

### 15. What are roles?

Roles organize reusable Ansible content into a standard folder structure.

### 16. What is Ansible Galaxy?

Ansible Galaxy is used to install, create, and share roles and collections.

## Advanced

### 17. What is variable precedence?

Variable precedence decides which value wins when the same variable is defined in multiple places. Extra vars passed with `-e` have very high precedence.

### 18. What is dynamic inventory?

Dynamic inventory gets hosts from external systems such as AWS, Azure, or a CMDB.

### 19. Difference between `include_tasks` and `import_tasks`?

`import_tasks` is static and parsed before execution. `include_tasks` is dynamic and evaluated during execution.

### 20. What is `serial`?

`serial` controls how many hosts are updated at a time, useful for rolling deployments.

### 21. What is `delegate_to`?

`delegate_to` runs a task on another host instead of the current target host.

### 22. What is `run_once`?

`run_once` runs a task only one time even when many hosts are targeted.

### 23. What is Ansible Vault?

Vault encrypts secret files or variables so sensitive values are not stored as plain text.

### 24. How do you troubleshoot an unreachable host?

Check inventory, SSH access, username, private key, firewall, DNS, and run `ansible all -m ping -vvv`.

### 25. How do you make playbooks production-ready?

Use roles, version control, syntax checks, check mode, clear variables, Vault for secrets, pinned Galaxy dependencies, and CI testing.

## Scenario Questions

### 26. Nginx should restart only when config changes. What do you use?

Use `notify` and a handler.

### 27. You need different package names for Ubuntu and Amazon Linux. What do you use?

Use facts with `when` conditions, or define OS-specific variables.

### 28. A task always reports changed even though it only checks status. What do you do?

Set `changed_when: false`.

### 29. A command returns non-zero but should not fail in one specific case. What do you do?

Use `failed_when` to define the exact failure condition.

### 30. How do you install dependencies for a project?

Use `requirements.yml` with `ansible-galaxy role install -r requirements.yml` or `ansible-galaxy collection install -r requirements.yml`.

