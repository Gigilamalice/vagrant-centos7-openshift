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
echo hello
echo ================================================================================

# fix locale warning
echo LANG=en_US.utf-8 >> /etc/environment
echo LC_ALL=en_US.utf-8 >> /etc/environment

# yum -y update
yum install -y wget
yum install -y git
# yum install -y nano tree zsh
# yum -y install python-pip

# Download openshift client oc
cd /tmp
wget $1 -o wget.log
tar xvzf openshift-origin-client-tool*
cd openshift-origin-client-tools*
cp oc  /usr/local/sbin

# Install Docker 
yum install -y docker device-mapper-libs device-mapper-event-libs
systemctl start docker.service
# systemctl status docker
systemctl enable docker.service
sudo chcon -t docker_exec_t /usr/bin/docker*
sudo systemctl restart docker

groupadd docker
usermod -aG docker vagrant

# Create local Repository
# yum -y install yum-utils


SCRIPT

$deployuservagrantscript = <<-SCRIPT
echo ================================================================================
echo I am testing oc and docker ...
echo
echo as user: $(whoami), at current path: $(pwd), on hostname: $(hostname)
echo with args passed: $1
echo  
echo ================================================================================
echo .
echo .
oc version | grep "oc"
echo
docker version | grep "version"

# sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "centos/7"
  # config.vm.box_version = "1811.02"
  config.vm.hostname = NAME
  config.vm.define NAME

  # config.vm.network "private_network", ip: "192.168.50.10"
  config.vm.synced_folder ".", "/vagrant"
  
  # Port forwarding
  config.vm.network "forwarded_port", guest: 22, host: 2220
  # Port for Local Docker Repository 
  config.vm.network "forwarded_port", guest: 5000, host: 5000
  # Port for Webgui Portainer
  config.vm.network "forwarded_port", guest: 9000, host: 9000
  # Port for Webgui Registry
  config.vm.network "forwarded_port", guest: 80, host: 8080


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
