# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

# look up system cpu and ram so we can use more intelligent defaults
LINUX = RUBY_PLATFORM =~ /linux/
OSX = RUBY_PLATFORM =~ /darwin/
if OSX
  CPUS = `sysctl -n hw.ncpu`.to_i
  MEM = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
elsif LINUX
  CPUS = `nproc`.to_i
  MEM = `sed -n -e '/^MemTotal/s/^[^0-9]*//p' /proc/meminfo`.to_i / 1024 / 4
end

# use (faster) nfs sharing on osx only
SHARING = OSX ? { nfs: true } : nil

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'bento/ubuntu-18.04'

  # Allow the project directory to be accessible inside the Vagrant box.
  # This should match the Ansible host_vars/vagrant synced_folder value.
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.synced_folder '.', '/mnt/vagrant', type: "nfs", mount_options: ['rw', 'vers=3', 'tcp'], linux__nfs_options: ['rw','no_subtree_check','all_squash','async']

  # Ideally, this IP will be unique, so the entry added to /etc/hosts won't
  # conflict with that of another project.
  config.vm.network :private_network, ip: '172.18.86.x'

  # Automatically add an entry to /etc/hosts for this Vagrant box (requires sudo).
  config.hostsupdater.aliases = ['wp-project.local']

  # give vm access to 1/4 total system memory and all cpu
  config.vm.provider 'virtualbox' do |v|
    v.customize ['modifyvm', :id, '--memory', MEM] if defined?(MEM)
    v.customize ['modifyvm', :id, '--cpus', CPUS] if defined?(CPUS)
    v.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end

  # A specific name looks much better than "default" in ansible output.
  config.vm.define 'vagrant'

  # The Vagrant ansible provisioner is used here for convenience. Instead of
  # the following code, the Vagrant box may be provisioned manually with
  # ansible-playbook (like in production), but adding this code saves the
  # trouble of having to run ansible-playbook manually after "vagrant up".
  config.vm.provision 'ansible' do |ansible|
    # Run init playbook (which runs base, configure, vagrant-link playbooks).
    ansible.playbook = 'build/ansible/init.yml'
  end
end
