# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Handle local proxy settings
  if Vagrant.has_plugin?("vagrant-proxyconf")
    if ENV["http_proxy"]
      config.proxy.http = ENV["http_proxy"]
    end
    if ENV["https_proxy"]
      config.proxy.https = ENV["https_proxy"]
    end
    if ENV["no_proxy"]
      config.proxy.no_proxy = ENV["no_proxy"]
    end

  end

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "ubuntu/trusty64"
  
  # Set hostname
  config.vm.hostname = "devstackjuno"
  
  # Provisioning steps
  config.vm.provision :shell, path: "bootstrap.sh", privileged: false

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.59.100"

  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Don't boot with headless mode
    vb.gui = true
    vb.memory = "12800"
    vb.cpus = 4
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "4096"]
  end

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
      sudo apt-get update
      sudo apt-get -y upgrade
      sudo apt-get -y install git
      git config --global url.https://git.openstack.org/.insteadOf git://git.openstack.org/
      sudo git config --global url.https://git.openstack.org/.insteadOf git://git.openstack.org/
      git clone https://git.openstack.org/openstack-dev/devstack
      sudo pip install numpy
      sudo pip install python-monascaclient
      cd devstack
      git checkout stable/liberty
      echo '[[local|localrc]]
ADMIN_PASSWORD=password
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD
SERVICE_TOKEN=$ADMIN_PASSWORD
LOGFILE=$DEST/logs/stack.sh.log
LOGDIR=$DEST/logs
LOG_COLOR=False
# The following two variables allow switching between Java and Python for the implementations
# of the Monasca API and the Monasca Persister. If these variables are not set, then the
# default is to install the Java implementations of both the Monasca API and the Monasca Persister.
# Uncomment one of the following two lines to choose Java or Python for the Monasca API.
MONASCA_API_IMPLEMENTATION_LANG=${MONASCA_API_IMPLEMENTATION_LANG:-java}
#MONASCA_API_IMPLEMENTATION_LANG=${MONASCA_API_IMPLEMENTATION_LANG:-python}
# Uncomment one of the following two lines to choose Java or Python for the Monasca Pesister.
MONASCA_PERSISTER_IMPLEMENTATION_LANG=${MONASCA_PERSISTER_IMPLEMENTATION_LANG:-java}
#MONASCA_PERSISTER_IMPLEMENTATION_LANG=${MONASCA_PERSISTER_IMPLEMENTATION_LANG:-python}
# This line will enable all of Monasca.
enable_plugin monasca https://git.openstack.org/openstack/monasca-api
' > local.conf
    ./stack.sh
  SHELL

end

