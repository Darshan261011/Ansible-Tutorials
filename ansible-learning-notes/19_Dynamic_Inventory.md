# Dynamic Inventory

## What Is Dynamic Inventory?

Dynamic inventory gets the list of managed nodes from an external source instead of a static file.

External sources can include:

- AWS EC2.
- Azure.
- Google Cloud.
- VMware.
- Kubernetes.
- CMDB systems.

## Why It Is Used In Cloud

Cloud servers are created and destroyed often. Manually updating `inventory.ini` becomes unreliable.

Dynamic inventory can group servers by:

- Tags.
- Region.
- Environment.
- Instance state.
- Application name.

## Static Vs Dynamic Inventory

| Topic | Static | Dynamic |
|---|---|---|
| Source | Manual file | Cloud/API/plugin |
| Best for | Small labs | Cloud environments |
| Updates | Manual | Automatic |
| Grouping | Manual | Based on metadata |

## AWS EC2 Inventory Concept

Install collection:

```bash
ansible-galaxy collection install amazon.aws
```

Example `aws_ec2.yml`:

```yaml
plugin: amazon.aws.aws_ec2
regions:
  - ap-south-1
filters:
  instance-state-name: running
keyed_groups:
  - key: tags.Environment
    prefix: env
hostnames:
  - private-ip-address
compose:
  ansible_host: private_ip_address
```

Test:

```bash
ansible-inventory -i aws_ec2.yml --graph
```

## Azure Inventory Concept

Install collection:

```bash
ansible-galaxy collection install azure.azcollection
```

Example concept:

```yaml
plugin: azure.azcollection.azure_rm
include_vm_resource_groups:
  - production-rg
auth_source: auto
keyed_groups:
  - key: tags.environment
    prefix: env
```

## Simple Example Structure

```text
inventory/
  production.aws_ec2.yml
  staging.aws_ec2.yml
group_vars/
  env_production.yml
  env_staging.yml
```

## Mini Practice

- Read the AWS EC2 inventory plugin documentation.
- Create a sample dynamic inventory config.
- Run `ansible-inventory --graph`.
- Group instances by a tag such as `Environment`.

