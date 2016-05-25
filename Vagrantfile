VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/wily64"
  config.vm.synced_folder "shared", "/home/vagrant/shared", nfs: true

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  # jupyter default port
  # config.vm.network :forwarded_port, guest: 8888, host: 8888

  config.vm.provider "virtualbox" do |vb|
    # Use RAM calculations by Stefan Wrobel https://stefanwrobel.com/how-to-make-vagrant-performance-not-suck
    host = RbConfig::CONFIG['host_os']
    # Give VM 1/4 system memory 
    if host =~ /darwin/
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024
    elsif host =~ /linux/
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i 
    elsif host =~ /mswin|mingw|cygwin/
      # Windows code via https://github.com/rdsubhas/vagrant-faster
      mem = `wmic computersystem Get TotalPhysicalMemory`.split[1].to_i / 1024
    end
    mem = mem / 1024 / 4
    vb.customize ["modifyvm", :id, "--memory", mem]

    vb.cpus = 2
    # improve network connectivity
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
  end

  config.vm.define 'databriefing' do |node|
    node.vm.hostname = 'databriefing.vm'
    node.vm.network :private_network, ip: '192.168.47.15'
  end

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    # ansible.verbose = "vvv"
  end
end

