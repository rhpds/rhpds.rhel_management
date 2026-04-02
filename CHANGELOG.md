# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-04-02

### Added
- Initial release of rhel_management collection
- `mcp_host` role for MCP server integration with Satellite
- `satellite_host` role for registering hosts to Satellite
- `satellite_reconfigure` role for reconfiguring Satellite
- `insights_host` role for Red Hat Insights integration
- Comprehensive README documentation for all roles
- Collection-level documentation

### Security
- Added `no_log: true` to all tasks handling sensitive credentials
- Changed auth.json permissions from 0644 to 0600 (mcp_host)
- Changed goose config/secret permissions from 0644 to 0600 (mcp_host)
- Removed hardcoded passwords from command line (satellite_reconfigure)
- Made GPG check optional with default to enabled (satellite_host)
- Added SSL verification controls with secure defaults
- Removed debug output of admin access tokens (satellite_reconfigure)

### Changed
- Replaced `<CHANGEME>` placeholders with mandatory variable validation
- Added proper idempotency checks with `changed_when` conditions
- Added `creates` parameter to installation tasks for idempotency
- Improved error handling across all roles
- Added proper backup before PostgreSQL data directory deletion (satellite_host)
- Enhanced Satellite hostname change with better error handling (satellite_reconfigure)
- Added check for existing admin tokens before creation (satellite_reconfigure)

### Fixed
- Fixed typo in template variable: `lightspeedclient_secret` → `lightspeed_client_secret`
- Fixed circular dependency in mcp_host defaults
- Added proper owner and group settings to all file operations
- Improved container command handling to support conditional SSL verification
- Fixed template variable quoting in goose_config.j2
- Added proper failed_when conditions for better error handling

## [Unreleased]

### Planned
- Add molecule tests for all roles
- Add CI/CD pipeline with GitHub Actions
- Add example playbooks directory
- Add troubleshooting guides
- Add support for RHEL 10
