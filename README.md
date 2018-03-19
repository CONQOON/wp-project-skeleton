# CONQOON wordpress project skeleton

## About
The main part of this project skeleton is based on roots/bedrock
https://github.com/roots/bedrock/blob/master/composer.json

## Setup your project

### Install Vagrant plugins
This has to be done only once. If you've already installed the following plugins, you don't have to do it again.

#### vagrant-hostsupdater Plugin
This plugin updates your local /etc/hosts file automatically so that the vagrant box is reachable via the specified local
domain

`vagrant plugin install vagrant-hostsupdater`

#### vagrant-notify-forwarder plugin
This plugin is important if you want to use things like hot reload or in general when it is important, that your vagrant
hosts gets notified when somthing changes in your shared mounted directories

`vagrant plugin install vagrant-notify-forwarder`

### Install Ansible Galaxy Roles:

`ansible-galaxy install -r build/ansible/install_roles.yml -p build/ansible/roles/`

### Copying the SSL certificate to the local machine

`scp -i <route/to/your/project>/.vagrant/machines/vagrant/virtualbox/private_key vagrant@<project_name>.local:/etc/ssl/<project_name>.local.crt <local/target/path>`
