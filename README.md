# Ansible Collection - rhpds.rhel_management

[![License](https://img.shields.io/badge/license-GPL--2.0--or--later-blue.svg)](LICENSE)
[![Ansible](https://img.shields.io/badge/ansible-2.14+-blue.svg)](https://www.ansible.com/)

An Ansible collection for managing RHEL nodes with Red Hat Satellite, Red Hat Insights, and MCP (Model Context Protocol) integration.

## Description

This collection provides roles for managing Red Hat Enterprise Linux systems in various configurations:

- **mcp_host**: Configure RHEL hosts with MCP server integration for Satellite
- **satellite_host**: Register RHEL hosts to Red Hat Satellite
- **satellite_reconfigure**: Reconfigure Red Hat Satellite server settings
- **insights_host**: Register RHEL hosts with Red Hat Insights

## Requirements

- Ansible 2.14 or higher
- Python 3.6 or higher
- RHEL 8 or RHEL 9 target systems

### Collection Dependencies

This collection requires the following collections:

- `containers.podman` (>= 1.10.0)
- `community.general` (>= 5.0.0)
- `ansible.posix` (>= 1.4.0)

Install dependencies:

```bash
ansible-galaxy collection install -r requirements.yml
```

Create `requirements.yml`:

```yaml
---
collections:
  - name: containers.podman
    version: ">=1.10.0"
  - name: community.general
    version: ">=5.0.0"
  - name: ansible.posix
    version: ">=1.4.0"
```

## Installation

### From Ansible Galaxy

```bash
ansible-galaxy collection install rhpds.rhel_management
```

### From Source

```bash
git clone https://github.com/rhpds/rhel_management.git
cd rhel_management
ansible-galaxy collection build
ansible-galaxy collection install rhpds-rhel_management-*.tar.gz
```

## Roles

### mcp_host

Configure RHEL hosts with MCP (Model Context Protocol) server integration for Satellite.

**Key Features:**
- Podman authentication to Red Hat registries
- Goose CLI installation and configuration
- MCP server container deployment
- Satellite CA certificate management

[Full Documentation](roles/mcp_host/README.md)

### satellite_host

Register RHEL hosts to Red Hat Satellite.

**Key Features:**
- Subscription manager registration
- Katello CA consumer installation
- PostgreSQL initialization
- SSL verification support

[Full Documentation](roles/satellite_host/README.md)

### satellite_reconfigure

Reconfigure Red Hat Satellite server settings.

**Key Features:**
- Admin password management
- Hostname configuration
- Manifest refresh
- API token generation

[Full Documentation](roles/satellite_reconfigure/README.md)

### insights_host

Register RHEL hosts with Red Hat Insights.

**Key Features:**
- Red Hat Insights registration via rhc
- Malware detection configuration
- Subscription manager integration

[Full Documentation](roles/insights_host/README.md)

## Quick Start

### Example: Register Hosts to Satellite

```yaml
---
- name: Register hosts to Satellite
  hosts: rhel_servers
  become: true
  
  vars:
    satellite_org: "MyOrganization"
    satellite_activation_key: "my-activation-key"
    subdomain: "example.apps.cluster.com"
  
  roles:
    - rhpds.rhel_management.satellite_host
```

### Example: Configure MCP Host

```yaml
---
- name: Configure MCP host
  hosts: mcp_hosts
  become: true
  
  vars:
    registry_credentials_username: "{{ lookup('env', 'RH_USERNAME') }}"
    registry_credentials_password: "{{ lookup('env', 'RH_PASSWORD') }}"
    subdomain: "example.apps.cluster.com"
    student_name: "lab-user"
    satellite_reconfigure_admin_access_token: "your-token-here"
  
  roles:
    - rhpds.rhel_management.mcp_host
```

### Example: Complete Satellite Setup

```yaml
---
- name: Reconfigure Satellite
  hosts: satellite_servers
  become: true
  
  vars:
    common_password: "{{ vault_satellite_admin_password }}"
    subdomain: "example.apps.cluster.com"
    satellite_reconfigure_org: "Acme Org"
  
  roles:
    - rhpds.rhel_management.satellite_reconfigure

- name: Register clients to Satellite
  hosts: satellite_clients
  become: true
  
  vars:
    satellite_org: "Acme Org"
    satellite_activation_key: "rhel9-key"
    subdomain: "example.apps.cluster.com"
  
  roles:
    - rhpds.rhel_management.satellite_host
```

### Example: Register with Insights

```yaml
---
- name: Register with Red Hat Insights
  hosts: insights_hosts
  become: true
  
  vars:
    insights_org: "12345678"
    insights_activation_key: "insights-key"
  
  roles:
    - rhpds.rhel_management.insights_host
```

## Using Tags

All roles support tags for selective execution:

```bash
# Run only setup tasks
ansible-playbook playbook.yml --tags setup

# Run only install tasks
ansible-playbook playbook.yml --tags install

# Run only configure tasks
ansible-playbook playbook.yml --tags configure

# Skip container deployment
ansible-playbook playbook.yml --skip-tags container
```

### Available Tags by Role

**mcp_host:**
- `mcp_host`, `mcp_host_config`, `mcp_host_setup`, `mcp_host_install`, `mcp_host_configure`, `mcp_host_deploy`
- `podman`, `goose`, `python`, `certificates`, `container`, `tmux`

## Security Considerations

### Credentials Management

Never commit credentials to version control. Use Ansible Vault:

```bash
# Create encrypted file
ansible-vault create vars/secrets.yml

# Edit encrypted file
ansible-vault edit vars/secrets.yml

# Use in playbook
ansible-playbook playbook.yml --ask-vault-pass
```

Example `vars/secrets.yml`:

```yaml
---
registry_credentials_username: "myuser"
registry_credentials_password: "mypassword"
satellite_admin_password: "securepassword"
insights_activation_key: "secret-key"
```

### SSL Verification

All roles default to SSL verification enabled. Only disable for testing:

```yaml
# Satellite host with SSL disabled (NOT RECOMMENDED for production)
satellite_host_verify_ssl: false
satellite_host_disable_gpg_check: true

# MCP host with SSL disabled (NOT RECOMMENDED for production)
mcp_host_verify_ssl: false
```

### File Permissions

Roles automatically set secure permissions:
- Authentication files: `0600`
- Configuration files with secrets: `0600`
- Regular config files: `0644`
- Directories: `0755`

## Development

See [CONTRIBUTING.md](CONTRIBUTING.md) for development guidelines.

### Running Tests

```bash
# Lint all roles
ansible-lint roles/

# Test specific role
cd roles/mcp_host
molecule test
```

### Building the Collection

```bash
ansible-galaxy collection build
```

## Troubleshooting

### Common Issues

**Issue: Podman login fails**
```
TASK [mcp_host : Podman login to registry.redhat.io] ****
fatal: [host]: FAILED! => {"msg": "Error logging into registry"}
```

Solution: Verify credentials are correct and accessible.

**Issue: Satellite registration fails**
```
TASK [satellite_host : Register Nodes to Satellite] ****
fatal: [host]: FAILED! => {"msg": "Registration failed"}
```

Solution: Check activation key and organization name. Verify network connectivity to Satellite.

**Issue: PostgreSQL initialization fails**
```
TASK [satellite_host : Initialize PostgreSQL database] ****
fatal: [host]: FAILED! => {"msg": "initdb failed"}
```

Solution: Check existing data directory. Role will backup and retry automatically.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history and changes.

## License

GPL-2.0-or-later

## Author

Mitesh Sharma (mitsharm@redhat.com)

## Support

- **Issues**: https://github.com/rhpds/rhel_management/issues
- **Documentation**: https://github.com/rhpds/rhel_management
- **Source**: https://github.com/rhpds/rhel_management

---

**Red Hat Demo Platform (RHDP)**
