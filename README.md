# glusterfs_vagrant_libvirt_ansible

This Vagrant-libvirt setup creates three VMs and configures them as glusterfs
servers. It also creates a client VM that mounts the volume from the glusterfs
servers.

Default OS is openSUSE Leap 15.4. Although that can be changed in the
Vagrantfile, please beware that this will break the Ansible provisioning.

## Vagrant

1. You need vagrant obviously. And ansible. And git...
1. Fetch the box, per default this is `opensuse/Leap-15.4.x86_64`, using
   `vagrant box add opensuse/Leap-15.4.x86_64`.
1. Make sure the git submodules are fully working by issuing `git submodule init
   && git submodule update`
1. Run `vagrant up`
1. Log in on the client and check the directory `/opt/glusterfs/` with e.g. `df
   -h`. This should show that this is a mounted glusterfs volume:
   ```
   $ df -h /opt/glusterfs/
   Filesystem                                   Size  Used Avail Use% Mounted on
   glusterfs-server01:/vagrant_libvirt_ansible  8,4G  127M  8,3G   2%
   /opt/glusterfs
   $
   ```
1. Party!

## Disabling the Ansible provisioning

In case you do not want Ansible to install glusterfs (because you want to
install it yourself), just comment out the following lines in the `Vagrantfile`:

```hcl
        node.vm.provision "ansible" do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.groups = {
            "glusterfs_clients"  => [ "glusterfs-client01" ],
            "glusterfs_servers"  => [ "glusterfs-server01", "glusterfs-server02", "glusterfs-server03" ],
          }
          ansible.playbook = "ansible/playbook-vagrant.yml"
```

You also find all of the playbooks in the `ansible` folder.

## License

BSD-3-Clause

## Author Information

I am Johannes Kastl, reachable via kastl@b1-systems.de.
