# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL

  # Fix "stdin: is not a tty" message - https://github.com/mitchellh/vagrant/issues/1673
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
  config.vm.provision "shell", inline: <<-SHELL
    if [ -e "/etc/vagrant-provisioned" ];
    then
        echo "[vagrant provisioning] Vagrant provisioning already completed. Skipping..."
        exit 0
    else
        echo "[vagrant provisioning] Starting Vagrant provisioning process..."
    fi

    # Change the hostname so we can easily identify what environment we're on:
    HOSTNAME="barleypop-web"
    sudo hostname $HOSTNAME
    echo $HOSTNAME > /etc/hostname
    # Update /etc/hosts to match new hostname to avoid "Unable to resolve hostname" issue:
    echo "127.0.0.1 $HOSTNAME" >> /etc/hosts
    echo "[vagrant provisioning] Hostname set to @HOSTNAME"

    # install packages
    echo "[vagrant provisioning] Updating package lists"
    sudo apt-get update

    echo "[vagrant provisioning] Installing apache2"
    sudo apt-get install -y apache2
    if ! [ -L /var/www ]; then
      rm -rf /var/www
      ln -fs /vagrant /var/www
      # change Apache's default DocumentRoot from /var/www/html to /var/www/html 
      sed -i "s#DocumentRoot /var/www/html#DocumentRoot /var/www#g" /etc/apache2/sites-available/000-default.conf
    fi
    
    echo "[vagrant provisioning] Installing postfix"
    echo postfix postfix/mailname string $HOSTNAME | debconf-set-selections
    echo postfix postfix/main_mailer_type string 'Internet Site' | debconf-set-selections
    sudo apt-get install -y postfix
    service postfix reload

    echo "[vagrant provisioning] Installing php5"
    sudo apt-get install -y php5 libapache2-mod-php5

    echo "[vagrant provisioning] Installing git"
    sudo apt-get install -y git

    echo "[vagrant provisioning] Restarting apache2"
    sudo /etc/init.d/apache2 restart

    touch /etc/vagrant-provisioned
    echo " "
    echo "[vagrant provisioning] ##### Ready ######"
  SHELL

end
