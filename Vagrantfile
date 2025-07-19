
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 2.4.0"

# Inventory
HOSTS = {
  "control0" => {
    "ip"     => "192.168.56.2",
    "memory" => 4096,
    "cpus"   => 2,
    "role"   => "control_plane"
  },

  "node0" => {
    "ip"     => "192.168.56.3",
    "memory" => 2048,
    "cpus"   => 1,
  },

  # "node1" => {
  #   "ip"     => "192.168.56.4",
  #   "memory" => 2048,
  #   "cpus"   => 2,
  # }
}

# Vagrant configuration
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Base box settings
  config.vm.box         = "generic/debian12"
  config.vm.box_version = "4.3.12"

  # Define each VM
  HOSTS.each do |name, settings|
    config.vm.define name do |box|
      box.vm.hostname = "#{name}.k8s.local"
      box.vm.network  "private_network", ip: settings["ip"]

      # VirtualBox provider settings
      box.vm.provider "virtualbox" do |vb|
        vb.memory       = settings["memory"]
        vb.cpus         = settings["cpus"]
        vb.gui          = false
        vb.linked_clone = true
        vb.customize    ["modifyvm", :id, "--audio", "none"]
      end

      # Provision with Ansible on the last host
      if name == HOSTS.keys.last
        box.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.verbose            = true
          ansible.playbook           = "main.yml"
          ansible.galaxy_role_file   = "requirements.yml"
          ansible.galaxy_roles_path  = ".ansible/roles"
          ansible.groups = {
            "all"           => HOSTS.keys,
            "controls"      => HOSTS.select { |k, _| k.start_with?("control") }.keys,
            "nodes"         => HOSTS.select { |k, _| ! k.start_with?("control") }.keys,
            "controls:vars" => {
              "kubernetes_role" => "control_plane"
            },
            "nodes:vars" => {
              "kubernetes_role" => "node"
            }
          }
          ansible.limit = "all"
        end
      end
    end
  end
end
