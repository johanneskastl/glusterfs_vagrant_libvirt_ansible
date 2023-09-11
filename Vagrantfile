ENV['VAGRANT_NO_PARALLEL'] = 'yes'

Vagrant.configure("2") do |config|

  ###################################################################################
  # define number of glusterfs client VMs
  W = 1

  ###################################################################################
  # provision W VMs as clients
  (1..W).each do |i|

    # disable the /vagrant folder, as / is read-only
    config.vm.synced_folder ".", "/vagrant", disabled: true

    # name the VMs
    config.vm.define "glusterfs-client0#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.4.x86_64"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = true
        lv.memory = 1024
        lv.cpus = 1
      end

      # set the hostname
      node.vm.hostname = "glusterfs-client#{i}"

    end # config.vm.define clients

  end # each-loop clients

  ###################################################################################
  # define number of glusterfs client VMs
  S = 3

  ###################################################################################
  # provision S VMs as servers
  (1..S).each do |i|

    ###################################################################################
    # disable the /vagrant folder, as / is read-only
    config.vm.synced_folder ".", "/vagrant", disabled: true

    # name the VMs
    config.vm.define "glusterfs-server0#{i}" do |node|

      # which image to use
      node.vm.box = "opensuse/Leap-15.4.x86_64"

      # sizing of the VMs
      node.vm.provider "libvirt" do |lv|
        lv.random_hostname = true
        lv.memory = 2048
        lv.cpus = 1

        lv.storage :file,
          :type => 'qcow2',
          :size => '10G'
      end

      # set the hostname
      node.vm.hostname = "glusterfs-server0#{i}"

      # if this is the last machine
      if i == S

        #################################################
        # only target one node to not run ansible twice
        # but do not limit this to this node (set ansible.limit to all)
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "glusterfs_clients"  => [ "glusterfs-client01" ],
            "glusterfs_servers"  => [ "glusterfs-server01", "glusterfs-server02", "glusterfs-server03" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"

        end # node.vm.provision

      end # if-condition last machine

    end # config.vm.define servers

  end # each-loop servers

end # Vagrant.configure
