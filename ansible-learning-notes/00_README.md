# Ansible Learning Notes

These notes teach Ansible from the first installation step to advanced DevOps usage. The examples are Linux and Ubuntu focused, but most concepts work with any Linux distribution.

## What Is Ansible?

Ansible is an automation tool used to configure servers, install software, deploy applications, manage users, apply security settings, and run repeatable operational tasks.

You write the desired steps in YAML files called playbooks. Ansible connects to target servers over SSH and performs the tasks.

## Why Ansible Is Used

- No agent is required on managed servers.
- Automation is readable because playbooks use YAML.
- The same task can run on one server or hundreds of servers.
- It is useful for configuration management, deployments, patching, and repeatable operations.
- It works well with DevOps workflows and CI/CD pipelines.

## How To Study These Notes

1. Read the files in numeric order.
2. Type the commands yourself in a lab environment.
3. Start with ad-hoc commands before writing large playbooks.
4. Practice each mini task before moving forward.
5. Build the final project in `22_Real_World_Project.md`.

## Learning Path

| Stage | Files | What You Learn |
|---|---|---|
| Basics | 01 to 06 | Ansible concepts, installation, inventory, ad-hoc commands, YAML |
| Core Playbooks | 07 to 14 | Modules, variables, loops, handlers, templates, facts, tags, error handling |
| Reuse and Sharing | 15 to 18 | Roles, Galaxy, collections, Vault |
| Advanced Operations | 19 to 21 | Dynamic inventory, config file, advanced playbook patterns |
| Real Use | 22 to 26 | Project, interview questions, errors, cheatsheet, 20 complete playbook examples |

## Repository Structure

```text
ansible-learning-notes/
  00_README.md
  01_Introduction_to_Ansible.md
  ...
  25_Ansible_CheatSheet.md
  examples/
    inventory/
    playbooks/
    roles/
    templates/
    vault/
    project/
```

## Required Lab

Recommended lab:

- One Ubuntu control node where Ansible is installed.
- Two Ubuntu managed nodes reachable by SSH.
- A user with sudo access on all nodes.
- SSH keys configured from the control node to managed nodes.

Example hostnames used in these notes:

```text
control.example.com
web1.example.com
web2.example.com
```

## Mini Practice

- Create a folder named `ansible-lab`.
- Create an inventory file with two test hosts.
- Install Ansible on the control node.
- Run your first `ansible --version` command.
