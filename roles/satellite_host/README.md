# satellite_host

Register and configure RHEL hosts with Red Hat Satellite.

## Description

This role registers RHEL hosts to Red Hat Satellite by installing the katello-ca-consumer package, registering via subscription-manager, and initializing PostgreSQL database for Satellite services.

## Requirements

- RHEL 8 or 9
- Network access to Satellite server
- Valid Satellite organization and activation key

## Role Variables

### Required Variables

- `subdomain`: Environment subdomain for Satellite URL construction
- `satellite_host_org`: Satellite organization name
- `satellite_host_activation_key`: Satellite activation key

### Default Variables

```yaml
satellite_host_katello_ca_rpm: "http://satellite-{{ subdomain }}/pub/katello-ca-consumer-latest.noarch.rpm"
satellite_host_org: <CHANGEME>
satellite_host_activation_key: <CHANGEME>
satellite_host_verify_ssl: true
```

## Dependencies

None

## Example Playbook

```yaml
- hosts: satellite_clients
  become: true
  vars:
    subdomain: "example.apps.cluster.com"
    satellite_host_org: "Acme_Org"
    satellite_host_activation_key: "rhel8-dev"
    satellite_host_verify_ssl: true
  roles:
    - rhpds.rhel_management.satellite_host
```

## Tasks Performed

1. Unregister from subscription-manager (if previously registered)
2. Remove any existing katello-ca-consumer packages
3. Install katello-ca-consumer RPM from Satellite
4. Update CA trust store (if SSL verification enabled)
5. Register host to Satellite with activation key
6. Initialize PostgreSQL database
7. Enable and start PostgreSQL service

## Notes

- Set `satellite_host_verify_ssl: false` to skip SSL verification (not recommended for production)
- PostgreSQL initialization includes automatic retry logic if initial setup fails

## License

GPLv3

## Author

Mitesh Sharma (mitsharm@redhat.com)
