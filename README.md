# My Personal Infrastructure

> **⚠️ Important Notice**: This repository has been sanitized for public sharing. All personal information, sensitive data, and encrypted vault files have been removed. You will need to create your own `vault.yml` file with your personal secrets and update host-specific configurations before using these playbooks.

This repository contains the Ansible playbooks and roles I use to manage my personal infrastructure, primarily my home server (`thinkpad`). It follows the principles of Infrastructure as Code (IaC) to ensure a reproducible, version-controlled, and automated setup.

## Overview

The main goal of this project is to automate the configuration of a home media and utility server. The setup is heavily based on Docker, with various services running in containers.

The core components managed by these playbooks are:

*   **System Configuration**: Base system setup, user management, timezone, etc.
*   **Docker Environment**: Installation and configuration of Docker and Docker Compose.
*   **Containerized Services**: Deployment of various media and utility applications.
*   **Backup Strategy**: A multi-tiered backup system using `rsync` and `restic`.
*   **Reverse Proxy**: Nginx setup for accessing services.
*   **Scheduled Tasks**: Cron jobs for backups and system maintenance, with monitoring via Healthchecks.io.

## Technology Stack

*   **Configuration Management**: Ansible
*   **Containerization**: Docker
*   **Key Services**:
    *   **Media**: Plex, Sonarr, Radarr, Jackett, Prowlarr, Transmission, Tautulli, xTeVe
    *   **Photos**: Photoprism
    *   **Network**: Unifi Controller, DuckDNS
    *   **Monitoring**: Netdata
    *   **Backups**: Restic, rclone

## Repository Structure

```
infra/
├── group_vars/         # Variables for groups of hosts (e.g., all)
│   ├── all/
│   │   ├── vars.yml    # Common variables
│   │   └── vault.yml   # Encrypted secrets (using Ansible Vault)
├── playbooks/
│   └── main.yaml       # The main playbook to run
└── roles/              # Ansible roles for each component
    ├── system/
    ├── docker/
    ├── containers/
    ├── cron/
    ├── nginx/
    ├── restic_backup/
    └── ...
```

*   `playbooks/main.yaml`: The main entry point that applies roles to hosts.
*   `group_vars/`: Contains all the variables. Sensitive data like passwords and API keys are stored in an encrypted `vault.yml` file.
*   `roles/`: Each role is responsible for a specific piece of the infrastructure (e.g., installing Docker, deploying Plex, setting up cron jobs).

## Setup and Usage

### Prerequisites

1.  **Ansible**: Ensure Ansible is installed on the machine you're running the playbooks from.
2.  **SSH Access**: You need passwordless SSH access to the target hosts (e.g., `thinkpad`, `nodes`) using SSH keys.

### Secrets Management

**Note**: The `group_vars/all/vault.yml` file has been removed for security reasons. You must create this file yourself.

This repository uses Ansible Vault to manage secrets. The file `group_vars/all/vault.yml` should be created and encrypted. It contains all `vault_*` variables referenced in `group_vars/all/vars.yml`.

1.  **Create a vault password file**:
    This file should be added to your `.gitignore` to prevent it from being committed to version control.
    ```bash
    echo "your-vault-password" > .ansible_vault_pass
    ```

2.  **Create and edit the vault file**:
    ```bash
    ansible-vault create group_vars/all/vault.yml --vault-password-file .ansible_vault_pass
    ```

3.  **Populate `vault.yml`** with your secrets:
    ```yaml
    vault_ssh_public_key: "ssh-rsa AAAA..."
    vault_pushbullet_key: "o.xxxxxxxxxx"
    # ... and so on for all other secrets
    ```

### Running the Playbook

To apply the configuration, run the main playbook. You will need to provide the vault password.

```bash
# Run against all hosts defined in your inventory
ansible-playbook playbooks/main.yaml --vault-password-file .ansible_vault_pass

# Run only specific tasks using tags
ansible-playbook playbooks/main.yaml --vault-password-file .ansible_vault_pass --tags "docker,plex"
```

## Author

*   Igor Oliveira
