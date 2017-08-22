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
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.network :forwarded_port, id: "ssh", guest: 22, host: 2222, auto_correct: true

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.11"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./", "/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
    vb.customize ["modifyvm", :id, "--name", "ubuntu-trusty64-php7"]

    # Customize the amount of memory on the VM:
    vb.memory = "2048"
    vb.cpus = 1

  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  config.vbguest.auto_update = false

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get dist-upgrade -y
    sudo apt-get install -y autoconf dpkg-dev file g++ gcc libc-dev libpcre3-dev make pkg-config re2c \
                            ca-certificates curl libedit2 libsqlite3-0 libxml2 xz-utils \
                            --no-install-recommends
    sudo mkdir -p /usr/local/etc/php/conf.d

    sudo apt-get install -y apache2 \
                            --no-install-recommends
    sudo sed -ri 's/^export ([^=]+)=(.*)$/: ${\1:=\2}\nexport \1/' /etc/apache2/envvars
    sudo a2dismod mpm_event
    sudo a2enmod mpm_prefork rewrite expires

    sudo apt-get install -y wget dirmngr gnupg2 \
                            --no-install-recommends
    sudo mkdir -p /usr/src/php
    cd /usr/src
    sudo wget -O php.tar.xz https://secure.php.net/get/php-7.1.8.tar.xz/from/this/mirror
    sudo apt-get install -y apache2-dev \
                            libcurl4-openssl-dev libedit-dev libsqlite3-dev libssl-dev libxml2-dev zlib1g-dev \
                            libjpeg-dev libpng-dev
                            --no-install-recommends
    export CFLAGS="-fstack-protector-strong -fpic -fpie -O2"
    export CPPFLAGS="$CFLAGS"
    export LDFLAGS="-Wl,-O1 -Wl,--hash-style=both -pie"
    sudo tar -Jxf /usr/src/php.tar.xz -C /usr/src/php --strip-components=1
    cd /usr/src/php
    gnuArch="$(dpkg-architecture -qDEB_BUILD_GNU_TYPE)"
    debMultiarch="$(dpkg-architecture -qDEB_BUILD_MULTIARCH)"
    sudo ./configure \
        --build="$gnuArch" \
        --with-config-file-path=/usr/local/etc/php \
        --with-config-file-scan-dir=/usr/local/etc/php/conf.d \
        --disable-cgi \
        --enable-ftp \
        --enable-mbstring \
        --enable-mysqlnd \
        --with-curl \
        --with-libedit \
        --with-openssl \
        --with-zlib \
        --with-pcre-regex=/usr \
        --with-libdir="lib/$debMultiarch" \
        --with-apxs2 \
        --with-pdo-mysql=mysqlnd \
        --with-png-dir=/usr \
        --with-jpeg-dir=/usr \
        --with-gd
    sudo make -j "$(nproc)"
    sudo make install
    sudo make clean
    sudo pecl update-channels
  SHELL
end
