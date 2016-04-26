# -*- mode: ruby -*-
# vi: set ft=ruby :

$gitlab_install = <<GITLAB_INSTALL
set -xv
sudo yum install curl policycoreutils openssh-server openssh-clients
sudo systemctl enable sshd
sudo systemctl start sshd
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
sudo firewall-cmd --permanent --add-service=http
sudo systemctl reload firewalld

curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
sudo yum -y install gitlab-ce
sudo gitlab-ctl reconfigure
GITLAB_INSTALL

$pe2016_install = <<PE2016_INSTALL
sudo /vagrant/pe/puppet-enterprise-2016.1.1-el-7-x86_64/puppet-enterprise-installer -a /vagrant/pe/answers.pe2016.umask.com
sudo firewall-cmd --add-port=8140/tcp
sudo firewall-cmd --add-port=61613/tcp
sudo firewall-cmd --add-port=8142/tcp
sudo firewall-cmd --add-service=https
sudo firewall-cmd --permanent --add-port=8140/tcp
sudo firewall-cmd --permanent --add-port=61613/tcp
sudo firewall-cmd --permanent --add-port=8142/tcp
sudo firewall-cmd --permanent --add-service=https
PE2016_INSTALL


Vagrant.configure(2) do |config|
  
  config.vm.box = "puppetlabs/centos-7.2-64-nocm"
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true
  config.vm.provision "shell",
  inline: "sed -ri 's/127\.0\.0\.1\s.*/127.0.0.1 localhost localhost.localdomain/' /etc/hosts"
  
  config.vm.define "pe" do |pe|
    pe.vm.hostname = "pe2016.umask.com"
    pe.hostmanager.aliases = %w(pe2016)
    pe.vm.network "private_network", ip: "192.168.100.40"
    pe.vm.provision "shell", inline: $pe2016_install
    pe.vm.provider "virtualbox" do |vb|
      vb.memory = 6144
      vb.cpus = 2
    end
  end
  
config.vm.define "gitlab" do |gl|
  gl.vm.hostname = "gitlab.umask.com"
  gl.hostmanager.aliases = %w(gitlab)
  gl.vm.network "private_network", ip: "192.168.100.41"
  gl.vm.provision "shell", inline: $gitlab_install
    gl.vm.provider "virtualbox" do |vb|
      vb.memory = 2048
      vb.cpus = 1
    end
  end
end

  
