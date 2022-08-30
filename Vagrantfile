# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANT_EXPERIMENTAL="disks"

Vagrant.configure("2") do |config|

  config.vm.box = 'centos/7'
#  config.vm.box_url = 'https://cloud.centos.org/centos/8/x86_64/images/CentOS-8-Vagrant-8.4.2105-20210603.0.x86_64.vagrant-virtualbox.box'
#  config.vm.box_download_checksum = 'dfe4a34e59eb3056a6fe67625454c3607cbc52ae941aeba0498c29ee7cb9ac22'
#  config.vm.box_download_checksum_type = 'sha256'

config.vm.define "server" do |server|
config.vm.synced_folder ".", "/vagrant", disabled: true
  server.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  server.vm.host_name = 'server'
  server.vm.network :private_network, ip: "192.168.56.7"
  server.vm.provision "shell", inline: <<-SHELL
  sudo -i
  cd
  yum install -y rpmdevtools rpm-build wget openssl-devel zlib-devel pcre-devel gcc redhat-lsb-core
  rpmdev-setuptree
  wget -c https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm
  rpm -i nginx-1.14.1-1.el7_4.ngx.src.rpm
  cd ~/rpmbuild
  rpmbuild -ba SPECS/nginx.spec

# create local repo
  yum install nginx createrepo -y
  mkdir -p /repos/CentOS/7/
  cp ~/rpmbuild/RPMS/x86_64/nginx-*.rpm /repos/CentOS/7/
  createrepo /repos/CentOS/7/
  touch /etc/yum.repos.d/local.repo && echo -e '[local]\nname=Local\nbaseurl=file:///repos/CentOS/7/\nenabled=1\ngpgcheck=0\n' >> /etc/yum.repos.d/local.repo
  yum repolist enabled
  yum provides nginx
  SHELL

end
end

