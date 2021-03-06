Vagrant.configure("2") do |config|
  os = "ubuntu/xenial64"

  ##
  # Network Setup
  ##
  net_ip = "192.168.50"

  ##
  # Provisioning Logic
  ##
  config.vm.define :master, primary: true do |master_config|
    master_config.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
        vb.cpus = 1
        vb.name = "master"
    end
      master_config.vm.box = "#{os}"
      master_config.vm.host_name = 'saltmaster.local'
      master_config.vm.network "private_network", ip: "#{net_ip}.10"
      master_config.vm.synced_folder "salt/formulas/", "/srv/salt/formulas/"
      master_config.vm.synced_folder "salt/pillar/", "/srv/salt/pillar/"

      master_config.vm.provision :salt do |salt|
        salt.master_config = "salt/etc/master"
        salt.master_key = "salt/keys/master_minion.pem"
        salt.master_pub = "salt/keys/master_minion.pub"
        salt.minion_key = "salt/keys/master_minion.pem"
        salt.minion_pub = "salt/keys/master_minion.pub"
        salt.seed_master = {
                            "minion1" => "salt/keys/minion1.pub",
                            "minion2" => "salt/keys/minion2.pub"
                           }

        salt.install_type = "stable"
        salt.install_master = true
        salt.run_highstate = true
        salt.no_minion = true
        salt.verbose = true
        salt.colorize = true
        salt.bootstrap_options = "-P -c /tmp"
        salt.install_type = :stable
      end

    end


    [
      ["minion1",    "#{net_ip}.11",    "1024",    os ],
      ["minion2",    "#{net_ip}.12",    "1024",    os ],
    ].each do |vmname,ip,mem,os|
      config.vm.define "#{vmname}" do |minion_config|
        minion_config.vm.provider "virtualbox" do |vb|
            vb.memory = "#{mem}"
            vb.cpus = 1
            vb.name = "#{vmname}"
        end
        minion_config.vm.box = "#{os}"
        minion_config.vm.hostname = "#{vmname}"
        minion_config.vm.network "private_network", ip: "#{ip}"


        minion_config.vm.provision :salt do |salt|
          salt.minion_config = "salt/etc/#{vmname}"
          salt.minion_key = "salt/keys/#{vmname}.pem"
          salt.minion_pub = "salt/keys/#{vmname}.pub"
          salt.install_type = "stable"
          salt.run_highstate = true
          salt.verbose = true
          salt.colorize = true
          salt.bootstrap_options = "-P -c /tmp"
          salt.install_type = :stable
        end
      end
    end
  end
