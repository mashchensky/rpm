# -*- mode: ruby -*-
# vim: set ft=ruby :
# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :centos => {
        :box_name => "centos/7",
        :ip_addr => '192.168.11.101'
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "256"]
          end

          box.vm.provision "file", source: "~/otus/RPM/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm", destination: "~/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm"

          box.vm.provision "shell", inline: <<-SHELL
#          mkdir -p ~root/.ssh
#          cp ~vagrant/.ssh/auth* ~root/.ssh
           yum install epel-release -y
           yum install fish wget -y -q
# Install tools for building rpm
           yum install rpmdevtools rpm-build -y -q
           yum install tree yum-utils mc gcc vim redhat-lsb-core -y -q
# Install tools for creating your own REPO
           yum install nginx -y
           yum install createrepo -y -q
           mkdir /usr/share/nginx/html/repo
           cp /home/vagrant/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm /usr/share/nginx/html/repo/
           createrepo /usr/share/nginx/html/repo/
           sed '/:80 default_server/a\        autoindex on;' /etc/nginx/nginx.conf>/etc/nginx/nginx.conf.tmp
           rm /etc/nginx/nginx.conf
           mv /etc/nginx/nginx.conf.tmp /etc/nginx/nginx.conf
           systemctl start nginx
           cat>/etc/yum.repos.d/otus.repo<<'EOF'
[otus]
name=otus-linux
baseurl=http://localhost/repo
gpgcheck=0
enabled=1
EOF
      SHELL
      end
  end
end
