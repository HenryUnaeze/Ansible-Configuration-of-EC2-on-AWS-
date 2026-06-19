# Ansible Project 1406

## Overview
This repository contains a small Ansible project for managing AWS EC2 instances and shutting down Ubuntu hosts.

## Repository Structure
- `inventory.ini` - Static inventory with EC2 hosts and SSH connection details.
- `PLAYBOOKS/ec2_create.yaml` - Playbook to create new EC2 instances in AWS.
- `PLAYBOOKS/shutdown_ubuntu.yaml` - Playbook to shut down Ubuntu hosts defined in the inventory.
- `group_vars/` - Group variables for inventory groups.
- `vault.pass` - Password file for Ansible Vault (if used).

## Inventory
The inventory file defines three EC2 hosts:
- `ansible_ec2_1` (`ansible_host=3.239.207.141`, `ansible_user=ec2-user`)
- `ansible_ec2_2` (`ansible_host=44.205.1.146`, `ansible_user=ubuntu`)
- `ansible_ec2_3` (`ansible_host=32.197.181.94`, `ansible_user=ubuntu`)

## Playbooks
### `PLAYBOOKS/ec2_create.yaml`
Creates three EC2 instances in `us-east-1` using the `amazon.aws.ec2_instance` module.
- Instances created:
  - `ansible_ec2_1` with image `ami-0521cb2d60cfbb1a6`
  - `ansible_ec2_2` with image `ami-0b6d9d3d33ba97d99`
  - `ansible_ec2_3` with image `ami-0b6d9d3d33ba97d99`
- Uses AWS credentials passed as variables: `aws_access_key` and `aws_secret_key`.
- Sets `assign_public_ip: true` and uses the `default` security group.

### `PLAYBOOKS/shutdown_ubuntu.yaml`
Shuts down all inventory hosts running Ubuntu using a local command:
- Runs `/sbin/shutdown -h now`
- Only applies when `ansible_distribution == "Ubuntu"`
- Uses `become: true` to run the shutdown command with elevated privileges.

## Requirements
- Ansible installed
- `amazon.aws` collection installed
- AWS credentials with permission to create EC2 instances
- SSH access to hosts listed in `inventory.ini`

## Usage
### Create EC2 instances
```bash
ansible-playbook PLAYBOOKS/ec2_create.yaml \
  -e aws_access_key=YOUR_AWS_ACCESS_KEY \
  -e aws_secret_key=YOUR_AWS_SECRET_KEY
```

### Shutdown Ubuntu hosts
```bash
ansible-playbook -i inventory.ini PLAYBOOKS/shutdown_ubuntu.yaml
```

## Notes
- The `ec2_create.yaml` playbook runs locally on the controller (`hosts: localhost`).
- If you use Ansible Vault, place secrets in `group_vars` or an encrypted file and provide the vault password from `vault.pass`.

### Step 1 — Get Your EC2 Public IPs
Go to AWS Console and grab the public IPs of your 3 Ansible instances:

```ssh-copy-id -f "-o IdentityFile ~/ansible_ec2.pem" ubuntu@32.197.181.94`

ssh-copy-id -f "-o IdentityFile ~/ansible_ec2.pem" ubuntu@44.205.1.146

ssh-copy-id -f "-o IdentityFile ~/ansible_ec2.pem" ec2_user@3.239.207.141
```

### Step 3 — Test Passwordless SSH

ssh ubuntu@32.197.181.94

### Before vs After
BEFORE ssh-copy-id:
ssh -i ~/ansible_ec2.pem ubuntu@3.239.207.141

AFTER ssh-copy-id:
ssh ubuntu@3.239.207.141
