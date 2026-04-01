# satellite_reconfigure

Reconfigure Red Hat Satellite hostname and admin credentials.

## Description

This role reconfigures an existing Satellite server by resetting the admin password, changing the hostname, and refreshing the subscription manifest. This is typically used when deploying Satellite from a golden image or template.

## Requirements

- RHEL 8 or 9
- Red Hat Satellite server installed
- Hammer CLI configured
- Valid Satellite subscription manifest

## Role Variables

### Required Variables

- `subdomain`: Environment subdomain for constructing new hostname
- `common_password`: New admin password to set

### Default Variables

```yaml
satellite_reconfigure_new_hostname: "{{ groups['satellites'][0].split('.')[0] }}-{{ subdomain }}"
satellite_reconfigure_username: admin
satellite_reconfigure_new_admin_password: "{{ common_password }}"
satellite_reconfigure_org: "Acme Org"
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: satellites
  become: true
  vars:
    subdomain: "example.apps.cluster.com"
    common_password: "NewSecurePassword123"
    satellite_reconfigure_org: "My_Organization"
  roles:
    - rhpds.rhel_management.satellite_reconfigure
```

## Tasks Performed

1. Reset Satellite admin password using foreman-rake
2. Update Hammer CLI configuration with new password
3. Check for fapolicyd rules file existence
4. Change Satellite hostname using satellite-change-hostname
5. Refresh subscription manifest

## Important Notes

- This role will change the Satellite hostname - ensure DNS records are updated accordingly
- The hostname change includes `--skip-dns` flag - manual DNS configuration may be required
- Manifest refresh requires valid Red Hat subscription
- Downtime may occur during hostname change operation

## Return Code Handling

The role validates successful hostname change by checking for "Success!" in command output.

## License

GPLv3

## Author

Mitesh Sharma (mitsharm@redhat.com)
