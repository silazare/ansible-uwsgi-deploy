# -*- mode: ruby -*-
# vi: set ft=ruby :


# configuration settings
nodes = [
  {
    # appication servers
    :base_node_name => "application-",
    :image => "ubuntu/bionic64",
    :memory => 512,
    :base_ip_octet => 20,
    :group => "application",
    :count => 2
  },
  {
    # load balancers
    :base_node_name => "loadbalancer-",
    :image => "ubuntu/bionic64",
    :memory => 512,
    :base_ip_octet => 10,
    :group => "loadbalancer",
    :count => 2
  }
]

network_address = "192.168.200"
virtual_ip = "192.168.200.10"

Vagrant.configure("2") do |config|

  groups = {}    # ansible groups
  total_nodes = 0    # count of nodes to deploy
  deployed_nodes = 0    # count of deployed nodes

  # create ansible groups with node names
  nodes.each do |opts|
    # add node group to ansible group if doesn't exists
    if ! groups.has_key?(opts[:group])
      groups[opts[:group]] = []
      # calculate number of nodes to deploy
      total_nodes = total_nodes + opts[:count]
    end

    # create node name and add node to ansible group
    (1..opts[:count]).each do |node_id|
      full_node_name = "#{opts[:base_node_name]}#{node_id}"
      groups[opts[:group]].push(full_node_name)
    end
  end

  # deploy
  nodes.each do |opts|
    (1..opts[:count]).each do |node_id|
      full_node_name = "#{opts[:base_node_name]}#{node_id}"

      # deploy node
      config.vm.define "#{full_node_name}" do |node|
        node.vm.box = "#{opts[:image]}"
        node.vm.network "private_network", ip: "#{network_address}.#{opts[:base_ip_octet]+node_id}"
        node.vm.hostname = "#{full_node_name}"
        node.ssh.forward_agent = true
        node.vm.provider "virtualbox" do |vb|
          vb.memory = "#{opts[:memory]}"
        end

        # currently deployed nodes
        deployed_nodes = deployed_nodes + 1

        # RUN ANSIBLE ONLY WHEN ALL NODES UP
        if deployed_nodes == total_nodes
          node.vm.provision :ansible do |ansible|
            ansible.compatibility_mode = "2.0"
            ansible.groups = groups
            ansible.limit = "all"
            ansible.playbook = "provision.yml"
            ansible.raw_arguments = ['--diff', '-e ansible_python_interpreter=/usr/bin/python3']
            ansible.extra_vars = {
              virtual_ip: "#{virtual_ip}",
            }
          end
        end
      end
    end
  end
end
