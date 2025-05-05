# Ansible Role: OpenSSH

|Source|Version|CI|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-openssh)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-openssh)](https://github.com/grzegorzfranus/ansible-role-openssh/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-openssh/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-openssh/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures OpenSSH, a secure and widely-used implementation of the SSH protocol, for both server and client functionality. It focuses on implementing security best practices by default, providing a hardened SSH configuration while maintaining flexibility for customization.

## Main Actions

- Verify and handle system requirements
- Install OpenSSH packages
- Configure SSH server with security hardening
- Set up SELinux policies for OpenSSH (if applicable)
- Configure SSH client (optional)
- Deploy security-focused login banner
- Enable and manage the SSH service

## Requirements

### Supported operating systems
List of officially supported operating systems:
| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 22.04 (Jammy) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 11 (Bullseye) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Rocky Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Oracle Linux | 9 | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Ansible version

Ansible >= 2.15

### Python version

Python >= 3.9

### Setup module
The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, the role will not work properly.

### Root access
This role requires root access, so either configure it in your inventory files, run it in a playbook with a global `become: true` or invoke the role in your playbook like:
```yaml
- hosts: servers
  become: true
  roles:
    - role: grzegorzfranus.openssh
```

## Role Variables

### 1. General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_role_action` | Define which parts of the role to execute (Options: 'all', 'install', 'config') | `"all"` |
| `openssh_service_enabled` | Enable/disable SSH service on boot | `true` |

### 2. SSH Server Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_sshd_config_path` | SSH daemon configuration file path | `"/etc/ssh/sshd_config"` |
| `openssh_sshd_config_dir` | SSH daemon configuration directory for drop-in files | `"/etc/ssh/sshd_config.d"` |
| `openssh_port` | SSH daemon port | `22` |
| `openssh_protocol` | SSH protocol version | `2` |
| `openssh_listen_addresses` | List of IP addresses to listen on (empty for all) | `[]` |
| `openssh_tcp_forwarding` | Allow TCP forwarding | `false` |
| `openssh_agent_forwarding` | Allow agent forwarding | `false` |
| `openssh_x11_forwarding` | Allow X11 forwarding | `false` |
| `openssh_permit_tunnel` | Permit tunnel devices | `false` |
| `openssh_gateway_ports` | Allow remote hosts to connect to forwarded ports | `false` |
| `openssh_disable_forwarding` | Disable all forwarding features in one directive | `true` |
| `openssh_print_motd` | Print MOTD banner upon connection | `true` |
| `openssh_print_last_log` | Print last login information | `true` |
| `openssh_banner_path` | Path to banner file | `"/etc/issue.net"` |
| `openssh_client_alive_interval` | Session timeout in seconds | `180` |
| `openssh_client_alive_count_max` | Max dead client count before session termination | `2` |
| `openssh_max_sessions` | Maximum allowed sessions | `5` |
| `openssh_max_startups` | Maximum concurrent unauthenticated connections | `"5:50:10"` |

### 3. Authentication Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_permit_root_login` | Allow/deny root login (Options: 'yes', 'no', 'without-password', 'prohibit-password') | `"no"` |
| `openssh_permit_empty_passwords` | Allow empty passwords | `false` |
| `openssh_password_authentication` | Enable password authentication | `false` |
| `openssh_pubkey_authentication` | Enable public key authentication | `true` |
| `openssh_host_based_auth` | Enable host-based authentication | `false` |
| `openssh_ignore_rhosts` | Disable .rhosts files for security | `true` |
| `openssh_permit_user_environment` | Disable user environment file processing | `false` |
| `openssh_challenge_response_auth` | Enable challenge-response authentication | `false` |
| `openssh_kerberos_auth` | Enable Kerberos authentication | `false` |
| `openssh_kerberos_ticket_cleanup` | Clean up Kerberos tickets on connection close | `true` |
| `openssh_gssapi_auth` | Enable GSSAPI authentication | `false` |
| `openssh_gssapi_cleanup_credentials` | Clean up GSSAPI credentials on connection close | `true` |
| `openssh_allow_users` | List of users allowed to log in | `[]` |
| `openssh_allow_groups` | List of groups allowed to log in | `[]` |
| `openssh_deny_users` | List of users denied from logging in | `[]` |
| `openssh_deny_groups` | List of groups denied from logging in | `[]` |
| `openssh_authorized_keys_file` | Path to authorized_keys file | `".ssh/authorized_keys"` |
| `openssh_max_auth_tries` | Maximum authentication attempts | `3` |

### 4. Network Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_use_dns` | Use DNS to verify clients | `false` |
| `openssh_address_family` | Address family to use ('inet', 'inet6', 'any') | `"any"` |
| `openssh_accept_env` | Environment variables to accept from client | `["LANG", "LC_*"]` |

### 5. Security Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_strict_modes` | Enable strict mode (check file permissions) | `true` |
| `openssh_ciphers` | List of allowed ciphers | See below |
| `openssh_macs` | List of allowed MAC algorithms | See below |
| `openssh_kex_algorithms` | List of allowed key exchange algorithms | See below |
| `openssh_host_key_algorithms` | List of allowed host key algorithms | See below |
| `openssh_host_keys` | Paths to host keys | RSA and ED25519 only |
| `openssh_compression` | Enable/disable compression | `"no"` |
| `openssh_login_grace_time` | Login grace time in seconds | `60` |
| `openssh_use_pam` | Use PAM for authentication | `true` |
| `openssh_selinux_chroot_rw_homedirs` | Restrict SSH to home directories in SELinux | `false` |
| `openssh_selinux_sysadm_login` | Allow SSH to login as system administrator in SELinux | `false` |

**Default ciphers (strong security focus):**
```yaml
openssh_ciphers:
  - "chacha20-poly1305@openssh.com"
  - "aes256-gcm@openssh.com"
  - "aes128-gcm@openssh.com"
```

**Default MAC algorithms (encrypt-then-MAC only):**
```yaml
openssh_macs:
  - "hmac-sha2-512-etm@openssh.com"
  - "hmac-sha2-256-etm@openssh.com"
```

**Default key exchange algorithms (modern security):**
```yaml
openssh_kex_algorithms:
  - "curve25519-sha256@libssh.org"
  - "curve25519-sha256"
  - "diffie-hellman-group16-sha512"
  - "diffie-hellman-group18-sha512"
```

**Default host key algorithms (Ed25519 and strong RSA):**
```yaml
openssh_host_key_algorithms:
  - "ssh-ed25519"
  - "rsa-sha2-512"
  - "rsa-sha2-256"
```

### 6. Logging Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_log_level` | Verbosity of logging | `"VERBOSE"` |
| `openssh_syslog_facility` | Syslog facility to use | `"AUTH"` |

### 7. SSH Client Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_configure_client` | Enable/disable SSH client configuration | `true` |
| `openssh_ssh_config_path` | SSH client configuration path | `"/etc/ssh/ssh_config"` |
| `openssh_client_settings` | Dictionary of client settings | See below |

**Default client settings:**
```yaml
openssh_client_settings:
  # Connection settings
  connection_attempts: 2
  connect_timeout: 10
  server_alive_interval: 180
  server_alive_count_max: 2
  
  # Security settings
  check_host_ip: true
  strict_host_key_checking: "yes"
  update_host_keys: "yes"
  verify_host_key_dns: "yes"
  
  # Forwarding settings (disabled for security)
  forward_agent: false
  forward_x11: false
  
  # Global host settings
  global_known_hosts_file: "/etc/ssh/ssh_known_hosts"
  user_known_hosts_file: "~/.ssh/known_hosts"
  
  # Misc settings
  send_env: ["LANG", "LC_*"]
```

### 8. Custom Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_custom_options` | List of custom sshd_config options to add at the end of the file | `[]` |

Example usage:
```yaml
openssh_custom_options:
  - "ClientAliveInterval 300"
  - "ClientAliveCountMax 2"
  - "MaxStartups 10:30:100"
```

## Tags

- `always` - Tasks that always run (variable loading and validation)
- `setup` - Installation and initial setup tasks
- `prerequisites` - System requirement verification tasks
- `install` - Package installation tasks
- `selinux` - SELinux configuration tasks
- `security` - Security-related configuration tasks
- `configure` - Configuration tasks for SSH server and client
- `validate` - Variable validation tasks

## Example Playbooks

### Basic Setup

```yaml
---
- name: Install and configure OpenSSH
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.openssh
```

### Customized Security Configuration

```yaml
---
- name: Configure OpenSSH with enhanced security
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.openssh
      vars:
        # Disable root login entirely
        openssh_permit_root_login: "no"
        
        # Disable password authentication (only allow key-based)
        openssh_password_authentication: false
        openssh_pubkey_authentication: true
        
        # Restrict to specific user and group
        openssh_allow_users:
          - admin
          - deployment
        openssh_allow_groups:
          - sshusers
        
        # Use non-standard port
        openssh_port: 2222
        
        # Only allow IPv4
        openssh_address_family: "inet"
        
        # Enhanced logging for security monitoring
        openssh_log_level: "VERBOSE"
```

### Minimal Client-Only Configuration

```yaml
---
- name: Configure OpenSSH client only
  hosts: workstations
  become: true
  roles:
    - role: grzegorzfranus.openssh
      vars:
        # Only install and configure client
        openssh_role_action: "install"
        openssh_service_enabled: false
        openssh_configure_client: true
        openssh_client_settings:
          # Increase connection timeout
          connection_attempts: 5
          connect_timeout: 30
          # Security settings
          strict_host_key_checking: "ask"
          # No forwarding
          forward_agent: false
          forward_x11: false
```

## License

Apache-2.0

## Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).

## Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.