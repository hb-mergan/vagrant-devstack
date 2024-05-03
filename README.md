### Virtual Machine Setup for Devstack

This repository contains configuration files to set up a virtual machine using Vagrant and VirtualBox, and automates the installation and setup of Devstack.

### Overview

The `Vagrantfile` provided here automates the setup of an Ubuntu (Jammy Jellyfish) virtual machine that includes the necessary configurations to deploy OpenStack via Devstack.

### Prerequisites

- **Vagrant**: Ensure you have Vagrant installed on your machine.
- **VirtualBox**: VirtualBox is required as the virtualization software.
- **Vagrant Cachier Plugin (optional)**: This setup supports the Vagrant Cachier plugin for caching packages to speed up setup times. Install it via `vagrant plugin install vagrant-cachier` if needed.

### Configuration

Before spinning up the VM, you need to configure the network settings. Copy `config.yaml.sample` to `config.yaml` and modify it to set the following parameters:
- `vm_ip_address`: The IP address for the VM.
- `floating_range`: The floating IP range for Devstack networking.
