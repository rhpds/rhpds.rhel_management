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
# Required - must be set
student_name: lab-user
subdomain: "example.apps.cluster.com"
registry_credentials_username: "your-username"
registry_credentials_password: "your-password"

# Optional - have defaults
mcp_host_podman_username: "{{ registry_credentials_username }}"
mcp_host_podman_password: "{{ registry_credentials_password }}"
mcp_host_satellite_url: "https://satellite-{{ subdomain }}"
mcp_host_satellite_ca_cert_url: "https://satellite-{{ subdomain }}/unattended/public/foreman_raw_ca"
mcp_host_server_image: "registry.redhat.io/satellite/foreman-mcp-server-rhel9@sha256:..."
mcp_host_verify_installation: true  # Post-installation verification
mcp_host_verify_ssl: false  # SSL verification for lab environments
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

1. Verify SSH connectivity to target hosts
2. Download and install goose CLI tool
3. Authenticate Podman to registry.redhat.io
4. Install linux-mcp-server Python library (from source using npm build)
5. **Apply fakeredis compatibility fix** (see FAKEREDIS_FIX.md)
6. Configure tmux for better terminal experience
7. Create certificate directory structure
8. Download Satellite CA certificate
9. Configure rootless Podman for user
10. Deploy and start MCP server container
11. **Verify installation** (goose, linux-mcp-server, configs, container)

## Known Issues & Fixes

### FakeRedis Compatibility Issue

The `linux-mcp-server` package has a dependency conflict with newer versions of `fakeredis`. This role automatically applies a compatibility fix. 

**Documentation:**
- [FAKEREDIS_FIX.md](FAKEREDIS_FIX.md) - Technical details about the fix
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) - Common issues and solutions

**Quick Test After Installation:**
```bash
timeout 3 linux-mcp-server  # Should start without ImportError
goose session              # Should connect to all MCP extensions
```

**If the fix didn't work:**
1. Run the debug playbook: `ansible-playbook examples/debug_mcp_setup.yml -vv`
2. Check [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for your specific issue
3. Re-run the fix: `ansible-playbook playbook.yml --tags fakeredis_fix -vv`

## Ports

- 8080: MCP server HTTP port

## Tags

The role supports the following tags for selective execution:

```bash
# Run only the fakeredis fix
ansible-playbook playbook.yml --tags fakeredis_fix

# Run only verification
ansible-playbook playbook.yml --tags mcp_verify

# Skip verification
ansible-playbook playbook.yml --skip-tags mcp_verify

# Run SSH connectivity check
ansible-playbook playbook.yml --tags ssh_check
```

## Important Notes

### User Context
All file operations run with the correct user context (`student_name`). The role uses `become: true` and `become_user: "{{ student_name }}"` where needed to ensure:
- Files are created with correct ownership
- Installations go to the user's home directory (~/.local)
- No permission issues when the user tries to run goose or linux-mcp-server

### Python Version
The role **auto-detects the Python version** and constructs paths dynamically. No hardcoded Python versions.

### Idempotency
The role is idempotent - safe to run multiple times:
- Git clone uses `force: true` to update if needed
- FakeRedis patch checks if already applied before patching
- Container restarts only if changed
- Most tasks have `creates` checks or proper conditionals

## License

GPLv3

## Author

Mitesh Sharma (mitsharm@redhat.com)
