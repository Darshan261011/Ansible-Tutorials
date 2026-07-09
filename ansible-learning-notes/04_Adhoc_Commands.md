# Ad-Hoc Commands

## What Are Ad-Hoc Commands?

Ad-hoc commands are one-line Ansible commands used for quick tasks. They are useful for checks, small changes, and troubleshooting.

Syntax:

```bash
ansible <host-pattern> -i <inventory> -m <module> -a "<module arguments>"
```

## Ping Module

Checks Ansible connectivity, not ICMP ping.

```bash
ansible all -i inventory.ini -m ping
```

## Command Module

Runs a command without shell features like pipes and redirects.

```bash
ansible web -i inventory.ini -m command -a "uptime"
ansible web -i inventory.ini -m command -a "df -h"
```

## Shell Module

Use when you need shell features.

```bash
ansible web -i inventory.ini -m shell -a "ps aux | grep nginx"
ansible web -i inventory.ini -m shell -a "echo hello > /tmp/hello.txt"
```

Prefer `command` unless shell features are needed.

## Copy Module

```bash
ansible web -i inventory.ini -m copy -a "src=index.html dest=/tmp/index.html"
```

With sudo:

```bash
ansible web -i inventory.ini -b -m copy -a "src=index.html dest=/var/www/html/index.html"
```

## File Module

Create directory:

```bash
ansible web -i inventory.ini -b -m file -a "path=/opt/app state=directory mode=0755"
```

Remove file:

```bash
ansible web -i inventory.ini -b -m file -a "path=/tmp/old.txt state=absent"
```

## apt Module

Ubuntu package installation:

```bash
ansible web -i inventory.ini -b -m apt -a "name=nginx state=present update_cache=yes"
```

## yum Module

RHEL or Amazon Linux 2:

```bash
ansible web -i inventory.ini -b -m yum -a "name=httpd state=present"
```

## service Module

```bash
ansible web -i inventory.ini -b -m service -a "name=nginx state=started enabled=yes"
```

## user Module

```bash
ansible all -i inventory.ini -b -m user -a "name=deploy shell=/bin/bash groups=sudo append=yes"
```

## Real Examples

Check memory:

```bash
ansible all -i inventory.ini -m command -a "free -m"
```

Install Git:

```bash
ansible all -i inventory.ini -b -m apt -a "name=git state=present update_cache=yes"
```

Restart nginx:

```bash
ansible web -i inventory.ini -b -m service -a "name=nginx state=restarted"
```

## Mini Practice

- Run uptime on all hosts.
- Install `curl`.
- Create `/opt/devops`.
- Start and enable nginx.

