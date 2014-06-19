#  Licensed to the Apache Software Foundation (ASF) under one or more
#   contributor license agreements.  See the NOTICE file distributed with
#   this work for additional information regarding copyright ownership.
#   The ASF licenses this file to You under the Apache License, Version 2.0
#   (the "License"); you may not use this file except in compliance with
#   the License.  You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.

VAGRANTFILE_API_VERSION = "2"

# Valid roles are:
#   client - client machine
#   kdc - kerberos kdc
#   nn - HDFS NameNode
#   yarn - Yarn Resource Manager and MapReduce Job History Server
#   slave - HDFS DataNode & Yarn Node Manager
#   hive-db - Hive MetaStore backing mysql
#   hive-meta - Hive MetaStore
#   zk - Zookeeper Server

# For each node
nodes = [
  { :hostname => 'gw', :ip => '240.0.0.10', :roles => ['client']},
  { :hostname => 'nn', :ip => '240.0.0.11', 
    :roles => ['kdc', 'nn', 'yarn', 'hive-meta', 'hive-db', 'zk']},
  { :hostname => 'slave1', :ip => '240.0.0.12', :roles => ['slave']},
#  { :hostname => 'slave2', :ip => '240.0.0.13', :roles => ['slave']},
#  { :hostname => 'slave3', :ip => '240.0.0.14', :roles => ['slave']},
]

domain = "example.com"

# clients to install on the client machines
clients = ['hdfs', 'yarn', 'pig', 'hive', 'zk']

# security options
security = false
realm = "EXAMPLE.COM"

# memory options
vm_memory = 2048
server_mem = 300
client_mem = 200

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # All Vagrant configuration is done here. The most common configuration
  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "omalley/centos6_x64"

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", vm_memory]
  end

  config.vm.provider :vmware_fusion do |vm|
    vm.vmx["memsize"] = vm_memory
  end

  nodes.each do |node|
    config.vm.define node[:hostname] do |node_config|
      node_config.vm.hostname = node[:hostname] + "." + domain
      node_config.vm.network :private_network, ip: node[:ip]
      node_config.vm.provision "puppet" do |puppet|
        puppet.module_path = "modules"
        puppet.options = ["--libdir", "/vagrant", 
	   "--fileserverconfig=/vagrant/fileserver.conf"]
        puppet.facter = {
  	  "hostname" => node[:hostname],
	  "roles" => node[:roles],
          "nodes" => nodes,
	  "domain" => domain,
          "security" => security,
          "realm" => realm,
          "clients" => clients,
          "server_mem" => server_mem,
          "client_mem" => client_mem,
        }
      end
    end
  end

end