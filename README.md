Ansible Real-time Project
A simple Ansible automation setup to provision AWS EC2 instances and manage node lifecycles securely using Ansible Vault.

Repository Structure
.
├── .gitignore              # Ignores sensitive keys, vault password, and temp files
├── README.md               # Project documentation
├── ansible.cfg             # Default Ansible configuration
├── inventory.ini           # Server IP addresses and SSH connection settings
├── ec2_create.yaml         # Playbook: Provisions EC2 instances via AWS API
├── ec2_stop.yaml           # Playbook: OS-level conditional shutdown for Ubuntu hosts
├── group_vars/
│   └── all/
│       └── pass.yml        # Vault-encrypted file storing AWS Access Keys
└── vault.pass              # Local password file for Ansible Vault (not committed)

Requirements
Python 3.9+ with boto3 and botocore installed.

Ansible 2.15+ with the AWS collection installed:
ansible-galaxy collection install amazon.aws

An AWS SSH key pair (e.g., ansible-key-pair.pem) downloaded with restricted permissions:
chmod 400 ~/Downloads/ansible-key-pair.pem

Setup & Credentials
Create the Vault Password File:
echo "your_vault_password" > vault.pass

Store AWS Credentials in group_vars/all/pass.yml:
ec2_access_key: "YOUR_AWS_ACCESS_KEY"
ec2_secret_key: "YOUR_AWS_SECRET_KEY"

Encrypt Secrets with Vault:
ansible-vault encrypt group_vars/all/pass.yml --vault-password-file vault.pass

Usage
Provision EC2 Instances
To launch new instances locally via the AWS API:
ansible-playbook ec2_create.yaml --vault-password-file vault.pass

Test SSH Connection
Verify that Ansible can ping all inventory nodes over SSH:
ansible all_nodes -i inventory.ini -m ping

Gracefully Shutdown Ubuntu Hosts
To execute an OS shutdown on Ubuntu/Debian hosts while skipping other OS families:
ansible-playbook -i inventory.ini ec2_stop.yaml --vault-password-file vault.pass