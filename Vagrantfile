# -*- mode: ruby -*-
# vi: set ft=ruby :

ANSIBLE_PATH = "/ansible".freeze
VAULT_PASS_PATH = "/vault_pass".freeze

Vagrant.require_version ">= 1.8.0"
Vagrant.configure(2) do |config|
  config.vm.box = "bento/debian-8.2"
  config.vm.network "private_network", ip: "192.168.124.10"

  config.ssh.forward_agent = true

  if Vagrant::Util::Platform.windows? || ENV["NFS"] == "false"
    config.vm.synced_folder ".", ANSIBLE_PATH, enabled: true
	
	# Kludge - The vault key cannot have the execute permission
    config.vm.synced_folder "./playbooks/vault_pass", VAULT_PASS_PATH, enabled: true, :mount_options => ["dmode=777","fmode=666"]
  else
    config.vm.synced_folder ".", ANSIBLE_PATH, enabled: true, type: nfs
	
	# Kludge - The vault key cannot have the execute permission
    config.vm.synced_folder "./playbooks/vault_pass", VAULT_PASS_PATH, enabled: true, type: nfs, :mount_options => ["dmode=777","fmode=666"]
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box

    config.cache.synced_folder_opts = {
      type: :nfs,
      mount_options: ["rw", "vers=3", "tcp", "nolock"]
    } unless Vagrant::Util::Platform.windows?
  end

  config.vm.provider "virtualbox" do |vb|
    vb.linked_clone = true
    vb.cpus = 2
    vb.memory = 2048
  end

  config.vm.provider "parallels" do |pl|
    pl.update_guest_tools = true
    pl.use_linked_clone = true
    pl.cpus = 2
    pl.memory = 2048
  end

  config.vm.provision "shell", path: "init.sh"
end
