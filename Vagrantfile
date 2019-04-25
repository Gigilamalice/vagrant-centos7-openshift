# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# define hostname
NAME = "octool"

OCBIN="https://github.com/openshift/origin/releases/download/v3.7.2/openshift-origin-client-tools-v3.7.2-282e43f-linux-64bit.tar.gz"


$deployuserrootscript = <<-SCRIPT
echo ================================================================================
echo I am provisioning linux package and configuration ...
echo
echo as user: $(whoami), at current path: $(pwd), on hostname: $(hostname)
echo with args passed: $1
echo
echo ================================================================================
sleep 10
# yum update -y
# yum -y install python-pip -y
yum install -y wget
yum install -y git

# fix locale warning
echo LANG=en_US.utf-8 >> /etc/environment
echo LC_ALL=en_US.utf-8 >> /etc/environment

# Download OC client
cd /tmp
wget $1
tar xvzf openshift-origin-client-tool*
cd openshift-origin-client-tools*
cp oc  /usr/local/sbin

SCRIPT

$deployuservagrantscript = <<-SCRIPT
echo ================================================================================
echo I am provisioning co  ...
echo
echo as user: $(whoami), at current path: $(pwd), on hostname: $(hostname)
echo with args passed: $1
echo  
echo ================================================================================
oc

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos/7"
  # config.vm.box_version = "1811.02"
  config.vm.hostname = NAME
  config.vm.define NAME

  # config.vm.network "private_network", ip: "192.168.50.10"
  config.vm.synced_folder ".", "/vagrant"
  
  # Port forwarding
  #config.vm.network "forwarded_port", guest: 22, host: 2220

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = "2"
  end

  config.vm.provision "shell", privileged: true,  keep_color: true, inline: $deployuserrootscript, args:[OCBIN]
  config.vm.provision "shell", privileged: false, keep_color: true, inline: $deployuservagrantscript, args:[OCBIN]
  config.vm.provision "shell", inline: "echo 'INSTALLER: Installation complete, CentOS 7 ready to use!'"
  
  #config.vm.provision "shell", path: "scripts/install.sh"
  # To reload reboot guest 
  #config.vm.provision :reload

end
