# Introduction To Ansible

## What Is Ansible?

Ansible is an open-source automation tool. It helps you run the same operational task on many servers in a controlled and repeatable way.

Common tasks:

- Install packages.
- Configure services.
- Copy files.
- Create users.
- Deploy applications.
- Restart services only when needed.

## Why Ansible Is Used In DevOps

DevOps teams use Ansible because manual server work is slow and error-prone. With Ansible, server configuration becomes code.

Example manual task:

```bash
ssh ubuntu@web1
sudo apt update
sudo apt install nginx -y
sudo systemctl enable --now nginx
```

With Ansible, the same task can be stored in a playbook and reused.

## Push-Based Architecture

Ansible usually works in a push-based model:

1. You run Ansible from the control node.
2. The control node connects to managed nodes.
3. Tasks are pushed to the managed nodes.
4. Results are returned to the control node.

The managed servers do not continuously pull instructions.

## Agentless Model

Ansible does not require an agent service on managed Linux servers. It uses SSH and Python, which are commonly available on Linux systems.

Benefits:

- Less software to install.
- Less maintenance.
- Easier onboarding of new servers.

## SSH-Based Automation

For Linux, Ansible connects using SSH.

Basic connectivity test:

```bash
ssh ubuntu@web1.example.com
```

Ansible ping test:

```bash
ansible web1 -i inventory.ini -m ping
```

## Ansible Vs Shell Script

| Topic | Ansible | Shell Script |
|---|---|---|
| Readability | YAML is easier for operations | Can become complex |
| Idempotency | Many modules avoid repeated changes | Must be handled manually |
| Multi-server support | Built in | Must be scripted |
| Error reporting | Structured output | Depends on script |
| Reuse | Roles and variables | Functions and custom scripts |

Idempotency means running the same automation multiple times should not keep changing the server if the desired state already exists.

## Ansible Vs Terraform

| Topic | Ansible | Terraform |
|---|---|---|
| Main use | Configure systems | Provision infrastructure |
| Style | Procedural and declarative | Declarative |
| Example | Install nginx on a VM | Create the VM |
| State file | Not required for normal use | Required |
| Best together | Configure cloud instances after creation | Create cloud resources first |

Typical workflow:

1. Terraform creates EC2 instances.
2. Ansible configures packages, users, services, and app files.

## Real-World Use Cases

- Configure web servers.
- Patch Ubuntu servers.
- Create Linux users for developers.
- Deploy app releases.
- Manage `/etc/nginx/nginx.conf`.
- Install monitoring agents.
- Rotate certificates.
- Harden SSH settings.

## Mini Practice

- Write three manual tasks you do on Linux servers.
- Convert them into Ansible task ideas.
- Identify which tasks are better for Terraform and which are better for Ansible.

