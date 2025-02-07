# WebLogic Automation with Ansible

## Project Overview

This repository contains an automation solution for deploying and managing WebLogic Server environments using Ansible. The project includes configurations for Admin Server and Managed Server deployment, along with certificate management capabilities.

### Key Features

- Automated WebLogic installation and domain creation
- Configuration for both Admin and Managed Servers
- Certificate management and SSL setup
- Server lifecycle management (start/stop operations)
- Modular role-based architecture

## Repository Structure

```
ansible/
├── hosts                    # Inventory file defining server groups
├── main.yml                # Main playbook
└── roles/
    ├── setup-weblogic/     # WebLogic installation and configuration
    │   ├── tasks/
    │   │   ├── main.yml
    │   │   ├── install-weblogic.yml
    │   │   ├── configure-domain.yml
    │   │   ├── configure-managed.yml
    │   │   ├── start-weblogic.yml
    │   │   └── stop-weblogic.yml
    │   ├── templates/
    │   │   ├── create_domain.py.j2
    │   │   ├── create_node_managed.py.j2
    │   │   ├── oraInsta.loc.j2
    │   │   └── wls.rsp.j2
    │   └── vars/
    │       └── main.yml
    └── manage-cert/        # Certificate management role
        ├── tasks/
        │   ├── main.yml
        │   ├── create-certs.yml
        │   ├── install-cert.yml
        │   ├── restart-admin.yml
        │   └── restart-managed.yml
        └── vars/
            └── main.yml
```

## Prerequisites

- Ansible 2.9+
- Python 3.x
- WebLogic Server installation files
- JDK 8 installation files

## Configuration

### Server Groups

The inventory (`hosts`) file defines the following server groups:

```ini
[admin]      # WebLogic Admin Server
[managed]    # WebLogic Managed Server
[weblogic]   # Group containing both admin and managed
```

### Variables

Key variables can be configured in:

- `roles/setup-weblogic/vars/main.yml` for WebLogic configuration
- `roles/manage-cert/vars/main.yml` for certificate management

## Usage

### Basic Installation

```bash
ansible-playbook -i hosts main.yml
```

### Managing WebLogic State

To start WebLogic services:

```bash
ansible-playbook -i hosts main.yml -e "weblogic_state=started"
```

To stop WebLogic services:

```bash
ansible-playbook -i hosts main.yml -e "weblogic_state=stopped"
```

### Certificate Management

To manage SSL certificates:

```bash
ansible-playbook -i hosts main.yml --tags "certificates"
```

## Server Configuration

### Admin Server

- Default port: 7001
- Console URL: http://admin_server:7001/console

### Managed Server

- Default port: 8001
- Node Manager port: 5556

## Security Notes

- Default credentials are for development only
- Change all default passwords before production use
- Ensure proper file permissions for keystores and certificates

## Troubleshooting

Common issues and solutions:

1. **Connection Issues**

   - Verify SSH connectivity
   - Check firewall settings
   - Ensure correct host entries

2. **Installation Failures**

   - Verify disk space requirements
   - Check file permissions
   - Review installation logs

3. **Start/Stop Problems**
   - Check server status
   - Verify Node Manager configuration
   - Review server logs

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

This project is licensed under the MIT License.
