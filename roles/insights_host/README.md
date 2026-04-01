# insights_host

Register and configure RHEL hosts with Red Hat Insights.

## Description

This role unregisters hosts from subscription-manager, removes any existing Satellite consumer packages, and registers the host to Red Hat Insights using Red Hat Connector (rhc). It also configures malware detection settings.

## Requirements

- RHEL 8 or 9
- Valid Red Hat subscription
- Network access to Red Hat Insights services

## Role Variables

### Required Variables

- `insights_host_org`: Red Hat organization ID for Insights registration
- `insights_host_activation_key`: Activation key for Insights registration

### Default Variables

```yaml
insights_host_org: <CHANGEME>
insights_host_activation_key: <CHANGEME>
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: rhel_hosts
  become: true
  vars:
    insights_host_org: "1234567"
    insights_host_activation_key: "my-activation-key"
  roles:
    - rhpds.rhel_management.insights_host
```

## Tasks Performed

1. Unregister from subscription-manager (if previously registered)
2. Remove any existing katello-ca-consumer packages
3. Register host with Red Hat Insights using rhc
4. Configure Insights malware detection settings

## License

GPLv3

## Author

Mitesh Sharma (mitsharm@redhat.com)
