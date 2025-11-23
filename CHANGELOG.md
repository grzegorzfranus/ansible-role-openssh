# Changelog

All notable changes to this OpenSSH role will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.6] - 2025-11-23

### Fixed 🔧
- Ansible 2.20 compatibility: replaced deprecated `ansible_os_family` with `ansible_facts['os_family']` syntax
- Resolved deprecation warning: "INJECT_FACTS_AS_VARS default to `True` is deprecated"
- Updated fact references in `tasks/main.yml` and `tasks/selinux.yml` to use modern `ansible_facts` dictionary

### Compatibility 🔄
- Fully compatible with Ansible 2.15 through 2.20 and beyond
- Prepared for Ansible 2.24 where top-level fact injection will be removed

## [1.0.5] - 2025-09-05

### Added ✅
- Variable assertions coverage extended in `tasks/assert.yml`:
  - `openssh_allow_service_stop` (boolean)
  - `openssh_service_name` (non-empty string)
  - `openssh_packages` (non-empty list)
  - `openssh_sshd_validate_command` (string)
- Flow documentation block added to `tasks/algorithms.yml` for clarity.
- README updated to document OS-specific variables and validation command.

### Changed 🔄
- Standardized all task/handler names to "OpenSSH | section | description" and removed emojis from names.
- Aligned tags with the allowed set: replaced `prerequisites` with `requirements`; removed non-standard tags from includes in `tasks/main.yml`.
- Normalized handler name in `handlers/main.yml` (emoji removed).

### Fixed 🔧
- README: removed non-existent variable `openssh_protocol`.
- README: updated Tags section to match allowed tags.

### Quality Improvements 📈
- Lint clean on all edited files.
- Documentation synchronized with defaults and assertions (variables table now consistent).

### Notes 🗒️
- Role naming, tagging, and validation now align with repository Ansible rules.

## [1.0.4] - 2025-08-07

### Added ✅
- Linting configuration: role-local `.yamllint` with 200 column limit and ignores for workflows/Molecule
- Declared required collections in `meta/main.yml`: `ansible.posix`, `community.general`
- Algorithm/version awareness: dynamic probing of supported algorithms (`ssh -Q`) and auto-selection of effective `Ciphers`, `MACs`, `KexAlgorithms`, and `HostKeyAlgorithms` to prevent `sshd -t` failures on older versions

### Changed 🔄
- Service management: when `openssh_service_enabled: false`, the role now stops and disables the service; when true, enables and starts it
- Handler: removed enablement toggle from restart handler to limit handler to restart only
- SELinux configuration reworked to use idempotent modules (`community.general.sefcontext`, `community.general.seport`) and idempotent restorecon execution instead of shell pipes
- Removed obsolete `Protocol` directive from `sshd_config.j2`; protocol 2 is implicit in modern OpenSSH
- Pruned protocol-related defaults and assertions
- Safer service control: added `openssh_allow_service_stop` (default `false`) and protection from stopping SSH when connected over SSH
- Compliance toggle: `openssh_gateway_ports` is tri-state (`yes`/`no`/`clientspecified`) and validated accordingly; template renders the exact value
- Default `MaxStartups` relaxed from `"5:50:10"` to `"10:30:60"`

### Fixed 🔧
- Multiple long-line messages in assertions converted to folded scalars for YAML linting compliance
- FQCN normalization across role tasks where applicable

### Security 🛡️
- Replaced non-idempotent SELinux shell/command operations with proper module usage

### Notes 🗒️
- Molecule scenario files were not modified as per scope; `ansible-lint` syntax-check failures in Molecule are out-of-scope for this change

## [1.0.3] - 2025-06-25

### Changed 🔄
- **Professional Workflow Naming** - Renamed `galaxy.yml` to `publish-to-galaxy.yml` for better clarity
- **CI Pipeline Rebranding** - Renamed `ci.yml` to `test-and-validation.yml` with professional naming
- **Enhanced CI/CD Presentation** - Added emoji indicators throughout all GitHub Actions workflows
- **Improved Job Organization** - Renamed workflow jobs for better semantic meaning and clarity
- **Step Name Clarity** - Enhanced step descriptions with more specific and professional language

### Fixed 🔧
- **CI Badge Reference** - Updated README.md CI status badge to point to renamed `test-and-validation.yml` workflow
- **Documentation Consistency** - Fixed broken badge link that was still referencing old `ci.yml` workflow file
- **Ansible Lint Compliance** - Fixed line length warnings in tasks/assert.yml (5 long lines resolved)
- **YAML Formatting** - Improved multiline success messages for better readability and lint compliance
- **Trailing Spaces Cleanup** - Removed all trailing whitespace in tasks/assert.yml (11 lines cleaned)
- **Code Quality Standards** - Achieved 100% ansible-lint compliance with no warnings or errors

### Workflow Improvements 🚀
- **Galaxy Publishing**: Changed to `📦 Publish to Ansible Galaxy` with descriptive emoji
- **Testing Pipeline**: Updated to `🧪 Test & Validation Pipeline` for professional appearance
- **Job Naming**: Consistent professional naming across all workflows
- **Step Emojis**: Added relevant emoji to all workflow steps:
  - 📥 Check out the codebase
  - 🐍 Set up Python 3
  - 📦 Install dependencies / Install Ansible dependencies
  - 🔍 Lint code
  - 🧪 Molecule test
  - 🚀 Upload role to Ansible Galaxy / Run molecule test

### Task System Enhancements 🎯
- **Main Tasks Organization** - Added emoji to all main task orchestration steps:
  - 📂 OS-specific variables gathering
  - 🧪 Variable validation and requirements checking
  - 🛠️ System prerequisites verification
  - 📦 Package installation
  - 🛡️ SELinux configuration
  - 🌐 Service configuration
- **Validation System Overhaul** - Enhanced all 29 validation tasks with emoji and improved messaging:
  - 🧪 Added emoji to all assertion task names for visual consistency
  - Enhanced error feedback with better context and clarity
- **Task Enhancement Across All Files** - Updated all task files with professional emoji indicators:
  - 🔍 Status checking and verification tasks
  - 🛠️ Setup and configuration tasks
  - 📦 Installation and package management
  - 🌐 Network and service configuration
  - 📝 File and template deployment
  - 🚀 Service management and startup
  - 🛡️ Security and SELinux configuration

### Validation Framework Modernization 🧪
- **Assertion Module Upgrade** - Updated all assertions from `assert:` to `ansible.builtin.assert:` for better compatibility
- **Enhanced Error Messaging** - Improved all 33 assertion error messages with:
  - ❌ Visual error indicators with emoji
  - Variable context showing actual values: `'{{ variable | default('undefined') }}'`
  - Clear, descriptive error descriptions with proper guidance
- **Complete Success Feedback System** - Added comprehensive success messages to all 33 assertions with:
  - ✅ Visual success indicators with emoji
  - Real-time variable value display for verification
  - Detailed status information for complex configurations
  - Dynamic messaging for optional configurations (e.g., custom options)
- **100% Validation Coverage** - Every assertion now provides both error and success feedback
- **Debugging Improvements** - Removed `quiet: true` from all assertions for better execution feedback
- **Professional Error Handling** - Enhanced user experience with meaningful validation messages during role execution

### Code Quality and Standards 📈
- **When Statement Formatting** - Standardized all `when:` statements to use multiline format `when: >` for consistency
- **YAML Structure Compliance** - Improved indentation and formatting across all task files
- **Cross-File Consistency** - Aligned all task files with chrony role patterns and repository standards
- **Enhanced Maintainability** - Improved code readability and debugging capabilities throughout the role

### Documentation Improvements 📚
- **README Table Enhancement** - Updated documentation table header from "CI" to "Tests" for better clarity
- **Professional Naming** - Table now accurately reflects the test-and-validation workflow purpose
- **Badge Alignment** - Column header now matches the actual badge content and workflow name
- **User Experience** - Clearer understanding of what the status badge represents

### Repository Organization 📁
- **Workflow Consolidation**: Two professionally named workflows for complete CI/CD pipeline
- **Consistency**: Aligned all workflow naming and emoji usage for cohesive presentation
- **Enhanced Readability** - Improved visual organization of workflows with meaningful emoji indicators
- **Standards Compliance**: Aligned with repository emoji and English-only policies across all workflow files
- **Professional Presentation**: Consistent emoji usage and professional formatting throughout the CI/CD pipeline
- **Task Organization**: Professional emoji usage across all 6 task files for improved visual organization
