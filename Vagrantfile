# -*- mode: ruby -*-
# vi: set ft=ruby :

BOX_NAME = ENV['BOX_NAME'] || "ubuntu/trusty64"
BOX_URI = ENV['BOX_URI'] || "http://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
SSH_PRIVKEY_PATH = ENV["SSH_PRIVKEY_PATH"]

$script = <<SCRIPT
user="$1"
if [ -z "$user" ]; then
    user=vagrant
fi

wget -q -O - https://get.docker.io/gpg | apt-key add -
wget -qO- https://get.docker.com/ | sh

add-apt-repository ppa:chris-lea/node.js
apt-get update -q
apt-get install -q -y python-software-properties python g++ make software-properties-common nodejs

usermod -a -G docker "$user"
docker pull ubuntu
printf "#"'!'"/bin/bash\ndocker rm -f "'$'"(docker ps -a -q)" > /usr/bin/clearcontainers
chmod +x /usr/bin/clearcontainers
SCRIPT

Vagrant::Config.run do |config|
  config.vm.box = BOX_NAME
  config.vm.box_url = BOX_URI

  if SSH_PRIVKEY_PATH
      config.ssh.private_key_path = SSH_PRIVKEY_PATH
  end

  config.ssh.forward_agent = true
end

Vagrant::VERSION >= "1.1.0" and Vagrant.configure("2") do |config|
  config.vm.provider :virtualbox do |vb, override|
    override.vm.provision :shell, :inline => $script
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end
end
