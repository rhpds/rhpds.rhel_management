# Contributing to rhel_management Collection

Thank you for your interest in contributing to the rhel_management Ansible collection!

## Development Setup

1. Fork the repository
2. Clone your fork:
   ```bash
   git clone https://github.com/<your-username>/rhel_management.git
   cd rhel_management
   ```

3. Install development dependencies:
   ```bash
   pip install ansible-lint ansible-core molecule molecule-plugins[docker]
   ```

4. Create a feature branch:
   ```bash
   git checkout -b feature/<description>
   ```

## Coding Standards

### Ansible Best Practices

1. **Use Fully Qualified Collection Names (FQCN)**
   ```yaml
   # Good
   - ansible.builtin.file:
   
   # Bad
   - file:
   ```

2. **Add no_log to sensitive tasks**
   ```yaml
   - name: Handle credentials
     ansible.builtin.command: sensitive-command
     no_log: true
   ```

3. **Use changed_when for commands**
   ```yaml
   - name: Run command
     ansible.builtin.command: some-command
     changed_when: result.rc == 0
   ```

4. **Add tags to tasks**
   ```yaml
   - name: Install package
     ansible.builtin.dnf:
       name: package
     tags:
       - install
       - packages
   ```

5. **Use mandatory for required variables**
   ```yaml
   my_required_var: "{{ user_var | mandatory }}"
   ```

### File Permissions

- Auth files: `0600`
- Config files with secrets: `0600`
- Regular config files: `0644`
- Directories: `0755`
- Executables: `0755`

### Security Guidelines

1. Never log sensitive data (passwords, tokens, keys)
2. Use SSL verification by default (`validate_certs: true`)
3. Avoid `disable_gpg_check` unless absolutely necessary
4. Don't expose passwords in command line arguments
5. Set proper file ownership and permissions

## Code Quality

### Before Committing

1. **Run ansible-lint**:
   ```bash
   ansible-lint roles/
   ```

2. **Test your role**:
   ```bash
   molecule test -s <role-name>
   ```

3. **Check syntax**:
   ```bash
   ansible-playbook --syntax-check playbooks/test.yml
   ```

### Commit Messages

Follow conventional commit format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

Examples:
```
feat(mcp_host): add support for custom SSL certificates

Add ability to provide custom CA certificates for MCP server
integration instead of downloading from Satellite.

Closes #123
```

```
fix(satellite_host): prevent data loss during PostgreSQL init

Add backup before removing PostgreSQL data directory in rescue block.
This prevents accidental data loss if initialization fails.

Fixes #456
```

## Pull Request Process

1. Update CHANGELOG.md with your changes
2. Update documentation if adding new features
3. Ensure all tests pass
4. Request review from maintainers
5. Address review feedback

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Tested locally
- [ ] Added/updated tests
- [ ] ansible-lint passes
- [ ] Documentation updated

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-reviewed code
- [ ] Commented complex sections
- [ ] Updated CHANGELOG.md
- [ ] Updated documentation
```

## Role Development

### Role Structure

```
roles/<role_name>/
├── defaults/
│   └── main.yml          # Default variables
├── handlers/
│   └── main.yml          # Handlers (optional)
├── meta/
│   └── main.yml          # Role metadata
├── tasks/
│   └── main.yml          # Main tasks
├── templates/
│   └── *.j2              # Jinja2 templates
└── README.md             # Role documentation
```

### Variable Naming

- Prefix all role variables with role name: `<role_name>_<var_name>`
- Use descriptive names: `mcp_host_server_image` not `image`
- Document all variables in defaults/main.yml and README.md

### Documentation

Each role must include:
- Description
- Requirements
- Role Variables (with defaults and descriptions)
- Dependencies
- Example Playbook
- License
- Author Information

## Testing

### Molecule Tests

Create molecule scenarios for each role:

```bash
cd roles/<role_name>
molecule init scenario -r <role_name>
```

### Manual Testing

Test against:
- RHEL 8
- RHEL 9
- Different Satellite versions
- Various network conditions

## Getting Help

- Open an issue for bugs or feature requests
- Start a discussion for questions
- Contact maintainers: mitsharm@redhat.com

## Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Focus on what is best for the community
- Show empathy towards other contributors

Thank you for contributing!
