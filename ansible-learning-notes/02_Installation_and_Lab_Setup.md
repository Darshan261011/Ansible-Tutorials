# Installation And Lab Setup

## Control Node And Managed Node

- Control node: machine where Ansible is installed and commands are run.
- Managed node: server controlled by Ansible over SSH.

For Linux automation, install Ansible only on the control node.

## Install Ansible On Ubuntu

```bash
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

Check:

```bash
ansible --version
```

Alternative using Python pip:

```bash
python3 -m pip install --user ansible
python3 -m pip show ansible
```

## Install Ansible On Amazon Linux

Amazon Linux 2023:

```bash
sudo dnf update -y
sudo dnf install python3-pip -y
python3 -m pip install --user ansible
ansible --version
```

Amazon Linux 2:

```bash
sudo amazon-linux-extras enable ansible2
sudo yum install ansible -y
ansible --version
```

## Simple Lab

Recommended lab:

| Node | Purpose | Example IP |
|---|---|---|
| control | Runs Ansible | 192.168.56.10 |
| web1 | Managed Ubuntu server | 192.168.56.11 |
| web2 | Managed Ubuntu server | 192.168.56.12 |

Create an inventory:

```ini
[web]
web1 ansible_host=192.168.56.11 ansible_user=ubuntu
web2 ansible_host=192.168.56.12 ansible_user=ubuntu
```

## Passwordless SSH Setup

Generate key on control node:

```bash
ssh-keygen -t ed25519 -C "ansible-control"
```

Copy key:

```bash
ssh-copy-id ubuntu@192.168.56.11
ssh-copy-id ubuntu@192.168.56.12
```

Test:

```bash
ssh ubuntu@192.168.56.11
```

Run Ansible ping:

```bash
ansible all -i inventory.ini -m ping
```

## Sudo Access

Many tasks need root privileges. Test sudo:

```bash
ssh ubuntu@192.168.56.11
sudo whoami
```

If sudo requires a password, use:

```bash
ansible all -i inventory.ini -m ping --ask-become-pass
```

## Common Installation Issues

| Problem | Cause | Fix |
|---|---|---|
| `ansible: command not found` | Not installed or PATH issue | Reinstall or check PATH |
| SSH permission denied | Key not copied or wrong user | Run `ssh-copy-id` and verify `ansible_user` |
| Python error | Python missing on managed node | Install `python3` |
| Host key warning | First SSH connection | SSH once manually or manage host keys |

## Mini Practice

- Install Ansible on the control node.
- Create `inventory.ini`.
- Configure SSH keys.
- Run `ansible all -i inventory.ini -m ping`.

