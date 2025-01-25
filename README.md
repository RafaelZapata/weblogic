# WebLogic Automation with Ansible

## Project Overview

This repository contains a complete automation solution for deploying and managing a WebLogic Server environment using Ansible. The project includes configurations for an Admin Server and a Managed Server, orchestrated through a Docker-based Ansible Controller.

### Key Features

- Automated WebLogic domain creation for Admin and Managed Servers.
- Configuration of Node Manager for server lifecycle management.
- Secure communication using SSH between the Admin Server and Managed Server.
- Docker container as the Ansible Controller, simplifying deployment and ensuring a controlled environment.

## Repository Structure

```
├── ansible
│   ├── hosts                     # Ansible inventory file
│   ├── main.yml                  # Main playbook for executing tasks
│   └── roles
│       └── setup-weblogic        # Role for WebLogic configuration
│           ├── defaults
│           │   └── main.yml      # Default variables
│           ├── files             # Files used in the setup process
│           ├── handlers
│           │   └── main.yml      # Handlers for restarting services
│           ├── meta
│           │   └── main.yml      # Metadata for the role
│           ├── tasks
│           │   ├── create-domain.yml  # Task for creating the domain
│           │   ├── create-managed.yml # Task for creating the managed server
│           │   ├── install-weblogic.yml # WebLogic installation tasks
│           │   └── main.yml          # Task entrypoint
│           ├── templates          # Templates for WebLogic configuration
│           │   ├── create_domain.py.j2
│           │   ├── create_node_managed.py.j2
│           │   ├── oraInsta.loc.j2
│           │   └── wls.rsp.j2
│           ├── tests
│           │   ├── inventory       # Test inventory for validation
│           │   └── test.yml        # Playbook for testing
│           └── vars
│               └── main.yml        # Variables specific to the role
└── infrastructure
    ├── Dockerfile                 # Dockerfile for Ansible controller
    ├── id_rsa                     # Private SSH key (example placeholder)
    ├── id_rsa.pub                 # Public SSH key (example placeholder)
    └── Vagrantfile                # Vagrant configuration for virtual machines
```

## Setup Instructions

### Prerequisites

- Docker installed on the host machine.
- VirtualBox and Vagrant for creating the Admin Server and Managed Server VMs.

### Generating SSH Keys

Since private keys are not included in the repository, you need to generate SSH keys before starting:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" -f ansible/id_rsa -N ""
```

This command generates:

- A private key (`id_rsa`) used by the Ansible Controller.
- A public key (`id_rsa.pub`) to be added to the `authorized_keys` of the Admin and Managed Servers.

### Building the Docker Image

The Docker container serves as the Ansible Controller. To build it, run:

```bash
docker build -t ansible-controller .
```

### Running the Docker Container

Run the Ansible Controller container:

```bash
docker run -it --name ansible-controller -v ${PWD}/../ansible:/ansible ansible-controller
```

The `-v` flag ensures that the Ansible files are correctly mounted into the container.

### Setting Up the Virtual Machines

Use Vagrant to create the Admin Server and Managed Server VMs:

```bash
vagrant up
```

Ensure the Admin Server VM has a NAT network configuration for connectivity with the local host.

### Deploying WebLogic with Ansible

1. SSH into the Docker container:
   ```bash
   docker exec -it ansible-controller bash
   ```
2. Run the Ansible playbook:
   ```bash
   ansible-playbook -i ansible/hosts ansible/main.yml
   ```

## Explanation of the Code

### Dockerfile

The Dockerfile creates a containerized environment for the Ansible Controller. Key features include:

- Installation of Python and Ansible.
- Addition of the SSH key for secure communication with the VMs.
- Configuration of `/etc/hosts` for resolving VM hostnames.

### Ansible Playbooks and Roles

The Ansible playbooks and roles handle:

- Creating and configuring the WebLogic domain for the Admin and Managed Servers.
- Setting up Node Manager and secure communication.
- Deploying the `boot.properties` for seamless server startup.

### Vagrantfile

The Vagrantfile defines two VMs:

- **Admin Server:** Handles WebLogic Administration Console and domain creation.
- **Managed Server:** Joins the domain created by the Admin Server.

## Notes

- Ensure that the private key (`id_rsa`) remains secure and is not uploaded to any public repository.
- The NAT network ensures connectivity between the Admin Server and your local host machine, enabling access to the WebLogic console.

## Troubleshooting

- **Node Manager Issues:** Ensure that the `SecureListener` property is set to `false` in `nodemanager.properties` before starting Node Manager.
- **Managed Server Connection:** Verify that the Admin Server’s IP is correctly set in the Managed Server configuration.

## Contributing

Feel free to fork this repository and submit pull requests to enhance functionality or fix issues.

## License

This project is licensed under the MIT License.
