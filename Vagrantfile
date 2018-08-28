# -*- mode: ruby -*-
# vi: set ft=ruby :

name = "kafka"

vms = { "192.168.100.211" => "#{name}-1",
        "192.168.100.212" => "#{name}-2",
        "192.168.100.213" => "#{name}-3" }

Vagrant.configure("2") do |config|

  vms.each do |vm_ip, vm_name|

    config.vm.define vm_name do |node|

      node.vm.box_check_update = false
      node.vm.box = "bento/centos-7.5"
      node.vm.hostname = vm_name
      node.vm.network "private_network", ip: vm_ip
      # config.vm.synced_folder "../data", "/vagrant_data"

      node.vm.provider "virtualbox" do |v|
        # v.name = vm_name
        v.memory = 2048
        v.cpus = 1
      end

      node.vm.provision "shell", inline: <<-SHELL
        # 确保脚本执行权限
        chmod 755 /vagrant/.setting/*.sh

        # 设置基本配置
        /vagrant/.setting/config.sh

        # 添加用户，用户名：ifnoelse，密码：ifnoelse
        /vagrant/.setting/add_user.sh ifnoelse ifnoelse

        # 设置用户ssh key登陆
        /vagrant/.setting/ssh_auth.sh ifnoelse

        # 添加本地hosts解析
        for i in "#{vms.map{|k,v|"#{k}    #{v}"}.join('" "')}";do echo $i>>/etc/hosts;done

        sed -i '1d' /etc/hosts

        if [ "#{name}-1" == "#{vm_name}" ];then wget https://bootstrap.pypa.io/get-pip.py -O -|python && pip install ansible;fi

        yum install -y java
      SHELL
      # node.ssh.private_key_path = ".setting/private_key"
      # node.ssh.username = "ifnoelse"
    end
  end
end