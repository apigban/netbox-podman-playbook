# Netbox Deployment Playbook

This Ansible playbook automates the deployment of Netbox and its required components using Podman containers on RHEL 9.3.

## Prerequisites

- AlmaLinux 9.4 or RHEL 9.3 host
- Ansible core 2.14 or higher
- `containers.podman` collection installed
- Access to container registries (docker.io)

## Features

- Configures base RHEL 9.3 system using `apigban.rhel-9.3-base` role
- Creates isolated podman network (`ipam_net`) for Netbox components
- Sets up persistent storage volumes for Netbox components:
  - `netbox-postgres-data`: PostgreSQL database storage
  - `netbox-redis-data`: Redis data persistence
  - `netbox-media-files`: Netbox media files
  - `netbox-reports`: Netbox reports directory
  - `netbox-scripts`: Netbox scripts directory
- Deploys and configures prerequisite containers:
  - PostgreSQL database
  - Redis cache
  - Netshoot (network troubleshooting container)
- Deploys Netbox container
- Configures Netbox with environment variables and secrets
- Configures Netbox to use podman network and persistent storage volumes

## Configuration

1. Copy `inventory/sample-secrets.yaml` to `inventory/secrets.yml`
2. Update the secrets file with your desired credentials
3. Encrypt the secrets file using ansible-vault

## Usage

### To run the playbook:

```bash
ansible-navigator run --eei localhost/role-dev-ee -m stdout main.yml -i inventory/inventory.yml
```

### To revert the changes until the end of applying `apigban.rhel-9.3-base`:

```bash
ansible-navigator run --eei localhost/role-dev-ee -m stdout destroy-netbox-containers.yml -i inventory/inventory.yml
```

## Security Notes
* All sensitive information is stored in an encrypted secrets file
* Containers run with limited privileges
* Components are isolated in a dedicated podman network

## Volumes
The playbook creates the following persistent volumes:

- `netbox-postgres-data`: PostgreSQL database files
- `netbox-redis-data`: Redis persistence
- `netbox-media-files`: Netbox uploaded media
- `netbox-reports`: Custom reports storage
- `netbox-scripts`: Custom scripts storage

## Dependencies
- Role: apigban.rhel-9.3-base
- Collection: containers.podman