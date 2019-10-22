# -*- mode: ruby -*-
# # vi: set ft=ruby :

# For help on using kubespray with vagrant, check out docs/vagrant.md

require 'fileutils'

Vagrant.require_version ">= 2.0.0"

CONFIG = File.join(File.dirname(__FILE__), "vagrant/config.rb")

COREOS_URL_TEMPLATE = "https://storage.googleapis.com/%s.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json"

# Uniq disk UUID for libvirt
DISK_UUID = Time.now.utc.to_i

SUPPORTED_OS = {
  "coreos-stable"       => {box: "coreos-stable",      user: "core", box_url: COREOS_URL_TEMPLATE % ["stable"]},
  "coreos-alpha"        => {box: "coreos-alpha",       user: "core", box_url: COREOS_URL_TEMPLATE % ["alpha"]},
  "coreos-beta"         => {box: "coreos-beta",        user: "core", box_url: COREOS_URL_TEMPLATE % ["beta"]},
  "ubuntu1604"          => {box: "generic/ubuntu1604", user: "vagrant"},
  "ubuntu1804"          => {box: "generic/ubuntu1804", user: "vagrant"},
  "centos"              => {box: "centos/7",           user: "vagrant"},
  "centos-bento"        => {box: "bento/centos-7.6",   user: "vagrant"},
  "fedora"              => {box: "fedora/28-cloud-base",                user: "vagrant"},
  "opensuse"            => {box: "opensuse/openSUSE-15.0-x86_64",       user: "vagrant"},
  "opensuse-tumbleweed" => {box: "opensuse/openSUSE-Tumbleweed-x86_64", user: "vagrant"},
  "oraclelinux"         => {box: "generic/oracle7", user: "vagrant"},
}

$subnet = "192.168.80"

BOXES = [
    {
        :name => "k8s-master-1",
        :eth1 => "#{$subnet}.101",
        :mem => "2048",
        :cpu => "2",
        :iscsi => "false",
        :worker => "false",
        :etcd => "false",
        :master => "true"
    },
    {
        :name => "k8s-master-2",
        :eth1 => "#{$subnet}.102",
        :mem => "2048",
        :cpu => "2",
        :iscsi => "false",
        :worker => "false",
        :etcd => "false",
        :master => "true"
    },
    {
        :name => "k8s-etcd-1",
        :eth1 => "#{$subnet}.111",
        :mem => "2048",
        :cpu => "1",
        :iscsi => "true",
        :worker => "false",
        :etcd => "true",
        :master => "false"
    },
    {
        :name => "k8s-etcd-2",
        :eth1 => "#{$subnet}.112",
        :mem => "2048",
        :cpu => "1",
        :iscsi => "true",
        :worker => "false",
        :etcd => "true",
        :master => "false"
    },
    {
        :name => "k8s-etcd-3",
        :eth1 => "#{$subnet}.113",
        :mem => "2048",
        :cpu => "1",
        :iscsi => "true",
        :worker => "false",
        :etcd => "true",
        :master => "false"
    },
    {
        :name => "k8s-worker-sm-1",
        :eth1 => "#{$subnet}.121",
        :mem => "2048",
        :cpu => "1",
        :iscsi => "true",
        :worker => "true",
        :etcd => "false",
        :master => "false"
    },
    {
        :name => "k8s-worker-sm-2",
        :eth1 => "#{$subnet}.122",
        :mem => "2048",
        :cpu => "1",
        :iscsi => "true",
        :worker => "true",
        :etcd => "false",
        :master => "false"
    },
    {
        :name => "k8s-worker-sm-3",
        :eth1 => "#{$subnet}.123",
        :mem => "2048",
        :cpu => "1",
        :iscsi => "true",
        :worker => "true",
        :etcd => "false",
        :master => "false"
    },
    {
        :name => "k8s-worker-md-1",
        :eth1 => "#{$subnet}.131",
        :mem => "4096",
        :cpu => "2",
        :iscsi => "true",
        :worker => "true",
        :etcd => "false",
        :master => "false"
    },
    {
        :name => "k8s-worker-md-2",
        :eth1 => "#{$subnet}.132",
        :mem => "4096",
        :cpu => "2",
        :iscsi => "true",
        :worker => "true",
        :etcd => "false",
        :master => "false"
    },
    {
        :name => "k8s-worker-md-3",
        :eth1 => "#{$subnet}.133",
        :mem => "4096",
        :cpu => "2",
        :iscsi => "true",
        :worker => "true",
        :etcd => "false",
        :master => "false"
    },
    {
       :name => "k8s-worker-lg-1",
       :eth1 => "#{$subnet}.141",
       :mem => "8192",
       :cpu => "2",
       :iscsi => "true",
       :worker => "true",
       :etcd => "false",
       :master => "false"
    },
    {
       :name => "k8s-worker-lg-2",
       :eth1 => "#{$subnet}.142",
       :mem => "8192",
       :cpu => "2",
       :iscsi => "true",
       :worker => "true",
       :etcd => "false",
       :master => "false"
    },
    {
       :name => "k8s-worker-lg-3",
       :eth1 => "#{$subnet}.143",
       :mem => "8192",
       :cpu => "2",
       :iscsi => "true",
       :worker => "true",
       :etcd => "false",
       :master => "false"
    }
]

# Defaults for config options defined in CONFIG

# The first two nodes are kube masters
$kube_master_instances = 2
# The first three nodes are etcd servers
$etcd_instances = 3
# All nodes are kube nodes
$kube_node_instances = 6
$num_instances = BOXES.length
$master_name_prefix = "k8s-master"
$etcd_name_prefix = "k8s-etcd"
$worker_name_prefix = "k8s-worker"
$instance_name_prefix = "k8s-node"
$vm_gui = false
$vm_memory = 2048
$vm_cpus = 1
$shared_folders = {}
$forwarded_ports = {}
$os = "ubuntu1804"
$network_plugin = "flannel"
# Setting multi_networking to true will install Multus: https://github.com/intel/multus-cni
$multi_networking = false

# The following only works when using the libvirt provider
$kube_node_instances_with_disks = false
$kube_node_instances_with_disks_size = "25G"
$kube_node_instances_with_disks_number = 2
$override_disk_size = false
$disk_size = "25GB"
$local_path_provisioner_enabled = false
$local_path_provisioner_claim_root = "/opt/local-path-provisioner/"

$playbook = "cluster.yml"

host_vars = {}

if File.exist?(CONFIG)
  require CONFIG
end

$box = SUPPORTED_OS[$os][:box]
# if $inventory is not set, try to use example
$inventory = "inventory/sample" if ! $inventory
$inventory = File.absolute_path($inventory, File.dirname(__FILE__))

# if $inventory has a hosts.ini file use it, otherwise copy over
# vars etc to where vagrant expects dynamic inventory to be
if ! File.exist?(File.join(File.dirname($inventory), "hosts.ini"))
  $vagrant_ansible = File.join(File.dirname(__FILE__), ".vagrant", "provisioners", "ansible")
  FileUtils.mkdir_p($vagrant_ansible) if ! File.exist?($vagrant_ansible)
  if ! File.exist?(File.join($vagrant_ansible,"inventory"))
    FileUtils.ln_s($inventory, File.join($vagrant_ansible,"inventory"))
  end
end

if Vagrant.has_plugin?("vagrant-proxyconf")
    $no_proxy = ENV['NO_PROXY'] || ENV['no_proxy'] || "127.0.0.1,localhost"
    (1..$num_instances).each do |i|
        $no_proxy += ",#{$subnet}.#{i+100}"
    end
end

# Set nodes arrays and group by `master`, `etcd`, `worker`
$etcd_nodes = []
$master_nodes = []
$worker_nodes = []

(1..$num_instances).each do |box|
  if box[:etcd] == "true"
    $etcd_nodes.push("#{box[:name]}")
  end
  if box[:master] == "true"
    $master_nodes.push("#{box[:name]}")
  end
  if box[:worker] == "true"
    $worker_nodes.push("#{box[:name]}")
  end
end

$iscsi_script = <<-SHELL
  apt-get -y install nfs-common \
  && apt-get -y install open-iscsi
  echo "NEED_STATD=yes" >> /etc/default/nfs-common
  iscsiadm --mode node --logout
  # Init iSCSI start
  sudo -i
  apt-get -y install open-iscsi
echo "
InitiatorName=#{$InitiatorName}:true
" > /etc/iscsi/initiatorname.iscsi
  systemctl restart iscsid open-iscsi
  iscsiadm -m discovery --portal "#{$portal}:3260" --op=delete
  iscsiadm -m discovery -t st -p "#{$portal}"
  iscsiadm -m node --targetname "#{$InitiatorName}:true" --portal "#{$portal}:3260" --op=update --name node.startup --value automatic
  iscsiadm -m node --targetname "#{$InitiatorName}:true" --portal "#{$portal}:3260" --op=update --name "node.conn[0].startup" --value "automatic"
  iscsiadm -m node --targetname "#{$InitiatorName}:true" --portal "#{$portal}:3260" --login
  parted --script /dev/sdb "mklabel msdos"
  parted --script /dev/sdb "mkpart primary 0% 100%"
  mkdir -p /data
  yes | mkfs.ext4 /dev/sdb
  mount /dev/sdb /data
  echo "

  # iSCSI #{$InitiatorName}:true START
  /dev/sdb   /data  ext4  defaults,auto,_netdev 0 0
  # iSCSI #{$InitiatorName}:true END
  " >> /etc/fstab
  echo "/data mounted"
SHELL

Vagrant.configure("2") do |config|

  config.vm.box = $box
  if SUPPORTED_OS[$os].has_key? :box_url
    config.vm.box_url = SUPPORTED_OS[$os][:box_url]
  end
  config.ssh.username = SUPPORTED_OS[$os][:user]

  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  # always use Vagrants insecure key
  config.ssh.insert_key = false

  if ($override_disk_size)
    unless Vagrant.has_plugin?("vagrant-disksize")
      system "vagrant plugin install vagrant-disksize"
    end
    config.disksize.size = $disk_size
  end

  current_item = 0

  BOXES.each do |opts|
    config.vm.define vm_name = opts[:name] do |node|
    current_item = (current_item + 1)
    node.vm.hostname = vm_name

      if Vagrant.has_plugin?("vagrant-proxyconf")
        node.proxy.http     = ENV['HTTP_PROXY'] || ENV['http_proxy'] || ""
        node.proxy.https    = ENV['HTTPS_PROXY'] || ENV['https_proxy'] ||  ""
        node.proxy.no_proxy = $no_proxy
      end

      ["vmware_fusion", "vmware_workstation"].each do |vmware|
        node.vm.provider vmware do |v|
          v.vmx['memsize'] = opts[:mem]
          v.vmx['numvcpus'] = opts[:cpu]
        end
      end

      node.vm.provider :virtualbox do |vb|
        vb.gui = $vm_gui
        vb.memory = opts[:mem]
        vb.cpus = opts[:cpu]
        vb.linked_clone = true
        vb.customize ["modifyvm", :id, "--vram", "8"] # ubuntu defaults to 256 MB which is a waste of precious RAM
      end

      node.vm.provider :libvirt do |lv|
        lv.memory = $vm_memory
        lv.cpus = $vm_cpus
        lv.default_prefix = 'kubespray'
        # Fix kernel panic on fedora 28
        if $os == "fedora"
          lv.cpu_mode = "host-passthrough"
        end
      end

      if $kube_node_instances_with_disks
        # Libvirt
        driverletters = ('a'..'z').to_a
        node.vm.provider :libvirt do |lv|
          # always make /dev/sd{a/b/c} so that CI can ensure that
          # virtualbox and libvirt will have the same devices to use for OSDs
          (1..$kube_node_instances_with_disks_number).each do |d|
            lv.storage :file, :device => "hd#{driverletters[d]}", :path => "disk-#{i}-#{d}-#{DISK_UUID}.disk", :size => $kube_node_instances_with_disks_size, :bus => "ide"
          end
        end
      end

      if $expose_docker_tcp
        node.vm.network "forwarded_port", guest: 2375, host: ($expose_docker_tcp + i - 1), auto_correct: true
      end

      $forwarded_ports.each do |guest, host|
        node.vm.network "forwarded_port", guest: guest, host: host, auto_correct: true
      end

      node.vm.synced_folder ".", "/vagrant", disabled: false, type: "rsync", rsync__args: ['--verbose', '--archive', '--delete', '-z'] , rsync__exclude: ['.git','venv']
      $shared_folders.each do |src, dst|
        node.vm.synced_folder src, dst, type: "rsync", rsync__args: ['--verbose', '--archive', '--delete', '-z']
      end

      ip = opts[:eth1]
      node.vm.network :private_network, ip: ip

      # Disable swap for each vm
      node.vm.provision "shell", inline: "swapoff -a"

      host_vars[vm_name] = {
        "ip" => ip,
        "flannel_interface" => "eth1",
        "kube_network_plugin" => $network_plugin,
        "kube_network_plugin_multus" => $multi_networking,
        "download_run_once" => "True",
        "download_localhost" => "False",
        "download_cache_dir" => ENV['HOME'] + "/kubespray_cache",
        # Make kubespray cache even when download_run_once is false
        "download_force_cache" => "True",
        # Keeping the cache on the nodes can improve provisioning speed while debugging kubespray
        "download_keep_remote_cache" => "False",
        "docker_keepcache" => "1",
        # These two settings will put kubectl and admin.config in $inventory/artifacts
        "kubeconfig_localhost" => "True",
        "kubectl_localhost" => "True",
        "local_path_provisioner_enabled" => "#{$local_path_provisioner_enabled}",
        "local_path_provisioner_claim_root" => "#{$local_path_provisioner_claim_root}",
        "ansible_ssh_user" => SUPPORTED_OS[$os][:user]
      }

      # Only execute the Ansible provisioner once, when all the machines are up and ready.
      puts "Current item: #{current_item} from #{$num_instances}"
      if "#{current_item}" == "#{$num_instances}"
      puts "Ansible run on #{$num_instances}"
        node.vm.provision "ansible" do |ansible|
          ansible.playbook = $playbook
          $ansible_inventory_path = File.join( $inventory, "hosts.ini")
          if File.exist?($ansible_inventory_path)
            ansible.inventory_path = $ansible_inventory_path
          end
          ansible.become = true
          ansible.limit = "all,localhost"
          ansible.host_key_checking = false
          ansible.raw_arguments = ["--forks=#{$num_instances}", "--flush-cache", "-e ansible_become_pass=vagrant"]
          ansible.host_vars = host_vars
          #ansible.tags = ['download']
          ansible.groups = {
            "etcd" => $etcd_nodes,
            "kube-master" => $master_nodes,
            "kube-node" => $worker_nodes,
            "k8s-cluster:children" => ["kube-master", "kube-node"],
          }
        end
      end
      # Ansible end
    end
  end
end
