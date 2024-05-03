VAGRANTFILE_API_VERSION = "2"

require 'yaml'
if File.file?('config.yaml')
  conf = YAML.load_file('config.yaml')
else
  raise "Configuration file 'config.yaml' does not exist."
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = "jammy-server-cloudimg-amd64-vagrant"
    config.vm.box_url = "https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64-vagrant.box"

    if Vagrant.has_plugin?("vagrant-cachier")
        config.cache.scope = :box
        config.cache.synced_folder_opts = {
          owner: "_apt",
          group: "ubuntu",
          mount_options: ["dmode=777", "fmode=666"]
        }
    end

    if conf['vm_ip_address']
        vm_ip_address = conf['vm_ip_address']
    else
        raise "vm_ip_address is not defined in config.yaml."
    end

    if conf['floating_range']
        floating_range = conf['floating_range']
    else
        raise "floating_range is not defined in config.yaml."
    end

    config.vm.network "private_network", ip: conf["vm_ip_address"]

    # Ensure NAT is set up for internet access
    config.vm.network "public_network", type: "dhcp", use_dhcp_assigned_default_route: true

    config.vm.provider "virtualbox" do |vb|
        vb.gui = false
        vb.memory = "8192"
        vb.cpus = 4
        # Enable nested hardware virtualization. Allows VMs to host other VMs
        vb.customize ["modifyvm", :id, "--nested-hw-virt", "on"]
        # Allow all network traffic on NIC adapter 2
        vb.customize ["modifyvm", :id, "--nicpromisc2", "allow-all"]
    end

    config.vm.provision "shell", inline: <<-SHELL
    sudo useradd -s /bin/bash -d /opt/stack -m stack
    sudo chmod +x /opt/stack
    echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
    sudo -u stack -i /bin/bash << 'EOF'
        git clone https://opendev.org/openstack/devstack
        cd devstack
        cat > local.conf << 'EOCONF'
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=\$ADMIN_PASSWORD
RABBIT_PASSWORD=\$ADMIN_PASSWORD
SERVICE_PASSWORD=\$ADMIN_PASSWORD
HOST_IP=#{vm_ip_address}
SERVICE_IP=\$HOST_IP
FLOATING_RANGE=#{floating_range}
EOCONF
        ./stack.sh
EOF
SHELL
end