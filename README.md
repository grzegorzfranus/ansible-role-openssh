# Ansible Role: OpenSSH

|Source|Version|Tests|License|
|------|-------|-------|-------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-openssh)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-openssh)](https://github.com/grzegorzfranus/ansible-role-openssh/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-openssh/actions/workflows/test-and-validation.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-openssh/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures OpenSSH, a secure and widely-used implementation of the SSH protocol for both server and client functionality. It provides enterprise-grade security hardening by default while maintaining operational flexibility for various deployment scenarios.

## ✨ Features

- 🔐 **Security-First Approach**: Hardened SSH configuration with modern cryptographic standards
- 🧰 **Comprehensive Configuration**: Complete SSH server and client setup with extensive customization
- 🛡️ **Security Hardening**: Disabled insecure features, strong ciphers, and authentication controls
- 🌐 **Multi-Platform Support**: Full compatibility across Debian, Ubuntu, and RedHat-based systems
- 🚀 **Service Management**: Complete systemd service configuration and state management
- 📊 **Access Control**: User/group-based authentication and connection restrictions
- 🔑 **Authentication Methods**: Public key, password, and advanced authentication support
- 🛠️ **SELinux Integration**: Comprehensive SELinux policy management and configuration
- 📝 **Custom Banners**: Security-focused login banner deployment
- 🧪 **Validation Framework**: Extensive variable validation and system compatibility checks
- 🔄 **Zero-Downtime Updates**: Safe configuration updates with service restart handling
- 📦 **Client Configuration**: Optional SSH client configuration management

## 🎯 Architecture

The role provides a flexible SSH architecture supporting various deployment patterns:

- **Server Mode**: Secure SSH daemon for remote access
- **Client Mode**: SSH client configuration for outbound connections
- **Hybrid Mode**: Combined server and client functionality
- **Bastion Host**: Enhanced security for jump host configurations

```
SSH Clients ←→ OpenSSH Server ←→ Target Servers
   (remote)      (this host)        (backend)
```

## 📋 Requirements

- **Ansible**: 2.15 or higher
- **Network**: Secure network connectivity for SSH access
- **Privileges**: sudo/root access on target hosts

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
This role requires root access for some tasks. Make sure that you are using a user with root privileges.

## 🚀 Quick Start

### 1. Basic SSH Server Setup

```yaml
---
- name: Configure SSH Server
  hosts: all
  become: true
  roles:
    - role: grzegorzfranus.openssh
      vars:
        openssh_service_enabled: true
        openssh_permit_root_login: "no"
        openssh_password_authentication: false
```

### 2. Enhanced Security Configuration

```yaml
---
- name: Configure Hardened SSH Server
  hosts: servers
  become: true
  roles:
    - role: grzegorzfranus.openssh
      vars:
        openssh_port: 2222
        openssh_allow_users: ["admin", "operator"]
        openssh_max_auth_tries: 2
        openssh_client_alive_interval: 300
```

### 3. Run the playbook

```bash
ansible-playbook -i inventory openssh-setup.yml
```

## ⚙️ Configuration

### Default Configuration

The role comes with production-ready security defaults:

```yaml
# Security settings
openssh_permit_root_login: "no"
openssh_password_authentication: false
openssh_pubkey_authentication: true

# Connection settings
openssh_port: 22
openssh_client_alive_interval: 180
openssh_max_sessions: 5

# Cryptographic settings
openssh_ciphers:
  - "chacha20-poly1305@openssh.com"
  - "aes256-gcm@openssh.com"
```

### Advanced Configuration

Customize for specific security requirements:

```yaml
---
- name: Advanced OpenSSH Configuration
  hosts: all
  become: true
  vars:
    openssh_role_action: "all"
    openssh_configure_client: true
    openssh_allow_groups: ["sshusers"]
    openssh_deny_users: ["guest", "anonymous"]
    openssh_custom_options:
      - "MaxStartups 10:30:100"
      - "LoginGraceTime 30"
  roles:
    - role: grzegorzfranus.openssh
```

## 📊 Variables

### 1. General Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_role_action` | Define which parts of the role to execute (Options: 'all', 'install', 'config') | `"all"` |
| `openssh_service_enabled` | Enable/disable SSH service on boot | `true` |
| `openssh_allow_service_stop` | Allow stopping/disabling SSH when `openssh_service_enabled: false` (safe-guard, ignored when connection is SSH) | `false` |

### 2. SSH Server Settings

| Variable | Description | Default |
|----------|-------------|---------|
| `openssh_sshd_config_path` | SSH daemon configuration file path | `"/etc/ssh/sshd_config"` |
| `openssh_sshd_config_dir` | SSH daemon configuration directory for drop-in files | `"/etc/ssh/sshd_config.d"` |
| `openssh_port` | SSH daemon port | `22` |
| `openssh_sshd_validate_command` | Command used to validate sshd config (string with %s path placeholder) | `'/usr/sbin/sshd -t -f %s'` |
| `openssh_service_name` | SSH service name (OS-specific) | Debian: `ssh`, RedHat: `sshd` |
| `openssh_packages` | Package list (OS-specific) | Debian: `[openssh-server, openssh-client]`, RedHat: `[openssh-server, openssh-clients]` |
| `openssh_listen_addresses` | List of IP addresses to listen on (empty for all) | `[]` |
| `openssh_tcp_forwarding` | Allow TCP forwarding | `false` |
| `openssh_agent_forwarding` | Allow agent forwarding | `false` |
| `openssh_x11_forwarding` | Allow X11 forwarding | `false` |
| `openssh_permit_tunnel` | Permit tunnel devices | `false` |
| `openssh_gateway_ports` | GatewayPorts value (`yes`/`no`/`clientspecified`) | `"no"` |
| `openssh_disable_forwarding` | Disable all forwarding features in one directive | `true` |
| `openssh_print_motd` | Print MOTD banner upon connection | `true` |
| `openssh_print_last_log` | Print last login information | `true` |
| `openssh_banner_path` | Path to banner file | `"/etc/issue.net"` |
| `openssh_client_alive_interval` | Session timeout in seconds | `180` |
| `openssh_client_alive_count_max` | Max dead client count before session termination | `2` |
| `openssh_max_sessions` | Maximum allowed sessions | `5` |
| `openssh_max_startups` | Maximum concurrent unauthenticated connections | `"10:30:60"` |

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
| `openssh_ciphers` | List of allowed ciphers (auto-filtered against server supported set) | See below |
| `openssh_macs` | List of allowed MAC algorithms (auto-filtered) | See below |
| `openssh_kex_algorithms` | List of allowed key exchange algorithms (auto-filtered) | See below |
| `openssh_host_key_algorithms` | List of allowed host key algorithms (auto-filtered) | See below |
| `openssh_host_keys` | Paths to host keys | RSA and ED25519 only |
| `openssh_compression` | Enable/disable compression | `"no"` |
| `openssh_login_grace_time` | Login grace time in seconds | `60` |
| `openssh_use_pam` | Use PAM for authentication | `true` |
| `openssh_selinux_chroot_rw_homedirs` | Restrict SSH to home directories in SELinux | `false` |
| `openssh_selinux_sysadm_login` | Allow SSH to login as system administrator in SELinux | `false` |

Note: Algorithm lists are kept strict by default, but the role computes and uses effective lists based on what the target OpenSSH supports to avoid configuration validation failures on older versions.

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

## 🔍 Verification

After deployment, verify SSH configuration and connectivity:

### Check SSH Service Status

```bash
# Check service status
sudo systemctl status ssh

# Verify SSH configuration syntax
sudo sshd -t

# Check listening ports
sudo ss -tulpn | grep :22
```

### Test SSH Connectivity

```bash
# Test SSH connection (adjust port if needed)
ssh -p 22 user@hostname

# Verify SSH configuration
ssh -G hostname

# Test with verbose output
ssh -v user@hostname
```

### Security Validation

```bash
# Check SSH host keys
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub

# Verify configuration file permissions
ls -la /etc/ssh/

# Check active SSH sessions
sudo netstat -tnpa | grep 'ESTABLISHED.*:22'
```

## 🛡️ Security Features

- ✅ **Secure Default Configuration**: Hardened settings with security-first approach
- ✅ **Modern Cryptography**: ChaCha20-Poly1305, AES-GCM, and Ed25519 algorithms
- ✅ **Authentication Controls**: Public key authentication with password disable by default
- ✅ **Access Control**: User/group-based restrictions and connection limits
- ✅ **Network Security**: Disabled forwarding features and DNS security
- ✅ **Session Management**: Timeout controls and connection limits
- ✅ **SELinux Integration**: Comprehensive SELinux policy configuration
- ✅ **File Permissions**: Proper ownership and permissions for all SSH files
- ✅ **Audit Logging**: Verbose logging with syslog integration

### Enhanced Security Configuration

```yaml
---
- name: Maximum Security SSH Configuration
  hosts: critical_servers
  become: true
  vars:
    openssh_port: 2222
    openssh_permit_root_login: "no"
    openssh_password_authentication: false
    openssh_max_auth_tries: 2
    openssh_allow_groups: ["sshusers"]
    openssh_client_alive_interval: 300
    openssh_client_alive_count_max: 2
    openssh_log_level: "VERBOSE"
    openssh_use_dns: false
    openssh_strict_modes: true
  roles:
    - role: grzegorzfranus.openssh
```

## 📁 Project Directory Structure

```
ansible-role-openssh/
├── defaults/
│   └── main.yml             # Default variable definitions and secure configuration
├── handlers/
│   └── main.yml             # Service restart and reload handlers
├── meta/
│   └── main.yml             # Role metadata and dependency information
├── tasks/
│   ├── main.yml             # Main task orchestration and flow control
│   ├── assert.yml           # Variable validation and system compatibility checks
│   ├── prerequisites.yml    # System preparation and package repository setup
│   ├── install.yml          # OpenSSH package installation and verification
│   ├── configure.yml        # SSH server and client configuration management
│   └── selinux.yml          # SELinux policy configuration and management
├── templates/
│   └── ssh/
│       ├── banner.j2        # Security banner template
│       ├── ssh_config.j2    # SSH client configuration template
│       └── sshd_config.j2   # SSH daemon configuration template
└── vars/
    ├── main.yml             # Internal role variables and constants
    ├── debian.yml           # Debian-specific variables and package names
    └── redhat.yml           # RedHat-specific variables and package names
```

## 🏷️ Tags

- `always` - Tasks that always run (variable loading and validation)
- `setup` - Setup tasks including OS-specific variables, requirements, installation, and configuration
- `requirements` - System requirement verification and preparation tasks
- `install` - OpenSSH package installation tasks
- `configure` - SSH server and client configuration tasks
- `validate` - Variable validation and system compatibility checks

## 🧪 Testing

This role includes comprehensive Molecule tests that validate functionality across all supported operating systems:

- **Ubuntu 22.04 LTS** - Complete functionality testing
- **Ubuntu 24.04 LTS** - Complete functionality testing  
- **Debian 12** - Complete functionality testing
- **Debian 11** - Complete functionality testing
- **Rocky Linux 9** - Complete functionality testing

### Running Tests Locally

```bash
# Install testing dependencies
pip install molecule molecule-plugins[docker] ansible-lint

# Run all tests
molecule test

# Run tests for specific platform
MOLECULE_DISTRO=ubuntu2204 molecule test

# Test only linting
molecule lint
```

### Test Matrix

The test suite verifies:
- ✅ **Package Installation**: Correct OpenSSH package installation
- ✅ **Service Management**: Service enablement and startup
- ✅ **Configuration**: Proper SSH server and client configuration
- ✅ **Security Settings**: Cryptographic algorithms and authentication methods
- ✅ **Permissions**: File and directory security validation
- ✅ **SELinux**: SELinux policy configuration when applicable
- ✅ **Connectivity**: SSH service accessibility and functionality

## 🔧 CI/CD Integration

This role includes comprehensive GitHub Actions workflows for automated testing and deployment:

### Testing Pipeline 🧪
- **Workflow**: `.github/workflows/test-and-validation.yml`
- **Name**: `🧪 Test & Validation Pipeline`
- **Purpose**: Automated testing across multiple platforms
- **Triggers**: Push to main branch, pull requests
- **Features**:
  - Multi-platform testing (Ubuntu 22.04, 24.04, Debian 11, 12, Rocky Linux 9)
  - Ansible lint validation
  - Molecule test execution
  - Cross-platform compatibility verification

### Galaxy Publishing 📦
- **Workflow**: `.github/workflows/publish-to-galaxy.yml`
- **Name**: `📦 Publish to Ansible Galaxy`
- **Purpose**: Automated role publishing to Ansible Galaxy
- **Triggers**: Tagged releases (v*)

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

### High-Security Bastion Host

```yaml
---
- name: Configure Bastion Host with Maximum Security
  hosts: bastion_hosts
  become: true
  roles:
    - role: grzegorzfranus.openssh
      vars:
        # Non-standard port
        openssh_port: 2222
        
        # Strict authentication
        openssh_permit_root_login: "no"
        openssh_password_authentication: false
        openssh_max_auth_tries: 2
        openssh_login_grace_time: 30
        
        # Restricted access
        openssh_allow_groups: ["bastion-users"]
        openssh_max_sessions: 3
        openssh_max_startups: "3:50:5"
        
        # Enhanced monitoring
        openssh_log_level: "VERBOSE"
        openssh_client_alive_interval: 300
        openssh_client_alive_count_max: 2
        
        # Disabled features
        openssh_tcp_forwarding: false
        openssh_agent_forwarding: false
        openssh_x11_forwarding: false
        openssh_permit_tunnel: false
        
        # Strong cryptography only
        openssh_ciphers:
          - "chacha20-poly1305@openssh.com"
        openssh_macs:
          - "hmac-sha2-512-etm@openssh.com"
        openssh_kex_algorithms:
          - "curve25519-sha256@libssh.org"
        openssh_host_key_algorithms:
          - "ssh-ed25519"
```

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.
- For major changes, please open an issue first to discuss what you would like to change.

If you have questions or suggestions, feel free to open an issue or contact the author via GitHub.

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
