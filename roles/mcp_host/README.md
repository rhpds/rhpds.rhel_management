# mcp_host

Configure RHEL hosts with MCP (Model Context Protocol) server integration for Satellite.

## Description

This role installs and configures the goose CLI tool, sets up Podman authentication with Red Hat registries, installs the linux-mcp-server Python library, and deploys a containerized MCP server for Satellite integration.

## Requirements

- RHEL 8 or 9
- Podman installed
- Python pip installed
- Valid Red Hat registry credentials
- Network access to registry.redhat.io and GitHub

## Role Variables

### Required Variables

- `registry_credentials_username`: Red Hat registry username
- `registry_credentials_password`: Red Hat registry password
- `subdomain`: Environment subdomain for Satellite URL construction

### Default Variables

```yaml
mcp_host_podman_username: "{{ registry_credentials_username }}"
mcp_host_podman_password: "{{ registry_credentials_password }}"
mcp_host_satellite_url: "https://satellite-{{ subdomain }}"
mcp_host_satellite_ca_cert: "http://satellite-{{ subdomain }}/unattended/public/foreman_raw_ca"
mcp_host_server_image: "registry.redhat.io/satellite/foreman-mcp-server-rhel9@sha256:fd6b99a7c68025279ceeb0a27751039043706a0da2569e3b613752724bfc31a6"
```

## Dependencies

- containers.podman (>= 1.10.0)

## Example Playbook

```yaml
- hosts: mcp_hosts
  become: true
  vars:
    registry_credentials_username: "myuser"
    registry_credentials_password: "mypassword"
    subdomain: "example.apps.cluster.com"
  roles:
    - rhpds.rhel_management.mcp_host
```

## Tasks Performed

1. Download and install goose CLI tool
2. Authenticate Podman to registry.redhat.io
3. Install linux-mcp-server Python library
4. Create certificate directory structure
5. Download Satellite CA certificate
6. Deploy and start MCP server container

## Ports

- 8080: MCP server HTTP port

## License

GPLv3

## Author

Mitesh Sharma (mitsharm@redhat.com)
