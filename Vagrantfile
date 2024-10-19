MACHINES = {
  :mysql1 => {
        :box_name => "rockylinux/9",
        :vm_name => "mysql1",
        :net => [   
                    ["192.168.56.10", 2, "255.255.255.0",  "pv"], 
                    ["192.168.50.10", 8, "255.255.255.0"],
                ]
  },

  :mysql2 => {
       :box_name => "rockylinux/9",
       :vm_name => "mysql2",
       :net => [
                   ["192.168.56.20", 2, "255.255.255.0",  "pv"],
                   ["192.168.50.20",  8,  "255.255.255.0"],
               ]
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.define boxname do |box|
      config.ssh.insert_key=false
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxconfig[:vm_name]
      
      box.vm.provider "virtualbox" do |v|
        v.memory = 3072
        v.cpus = 2
       end

      boxconfig[:net].each do |ipconf|
        box.vm.network("private_network", ip: ipconf[0], adapter: ipconf[1], netmask: ipconf[2], virtualbox__intnet: ipconf[3])
      end

      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end

      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        systemctl restart sshd
      SHELL

      if boxconfig[:vm_name] == "mysql2"
        box.vm.provision "ansible" do |ansible|
         ansible.playbook = "ansible/mysql.yml"
         ansible.inventory_path = "ansible/hosts"
         ansible.host_key_checking = "false"
         ansible.limit = "all"
        end
       end

    end
  end
end