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
  config.vm.box = "xcoo/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
      vb.cpus="4" 
  #   # Customize the amount of memory on the VM:
    vb.memory = "4096"
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

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
   config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2

# need to make sure source.list have source code repo uncommented out
#sed -i 's/^# deb-src http:/deb-src http:/g" /etc/apt/source.list
sed -i 's/^# deb-src http:/deb-src http:/g' /etc/apt/sources.list
sudo apt update
sudo apt upgrade -y
sudo apt install -y \
    build-essential \
    cmake \
    cmake-curses-gui \
    git \
    clang \
    clang-format \
    llvm \
    llvm-dev \
    libboost-all-dev \
    libgoogle-glog-dev \
    golang \
    libssl-dev \
    libevent-dev \
    default-jre \
    default-jre-headless \
    default-jdk \
    default-jdk-headless \
    maven \
    libncurses5-dev \
    binutils-dev \
    google-perftools \
    libdouble-conversion-dev \
    libevent-dev \
    libgdal-dev \
    libgflags-dev \
    libgoogle-perftools-dev \
    libiberty-dev \
    libjemalloc-dev \
    liblz4-dev \
    liblzma-dev \
    libsnappy-dev \
    zlib1g-dev \
    autoconf \
    autoconf-archive \
    vim \
    fish
sudo apt build-dep -y thrift-compiler
VERS=0.10.0
wget http://apache.claz.org/thrift/$VERS/thrift-$VERS.tar.gz
tar xvf thrift-$VERS.tar.gz
pushd thrift-$VERS
./configure \
    --with-lua=no \
    --with-python=no \
    --with-php=no \
    --with-ruby=no \
    --prefix=/usr/local/mapd-deps
make -j $(nproc)
sudo make install
popd

VERS=1.11.3
wget --continue https://github.com/Blosc/c-blosc/archive/v$VERS.tar.gz
tar xvf v$VERS.tar.gz
BDIR="c-blosc-$VERS/build"
rm -rf "$BDIR"
mkdir -p "$BDIR"
pushd "$BDIR"
cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr/local/mapd-deps \
    -DBUILD_BENCHMARKS=off \
    -DBUILD_TESTS=off \
    -DPREFER_EXTERNAL_SNAPPY=off \
    -DPREFER_EXTERNAL_ZLIB=off \
    -DPREFER_EXTERNAL_ZSTD=off \
    ..
make -j $(nproc)
sudo make install
popd

VERS=2017.04.10.00
wget --continue https://github.com/facebook/folly/archive/v$VERS.tar.gz
tar xvf v$VERS.tar.gz
pushd folly-$VERS/folly
/usr/bin/autoreconf -ivf
./configure --prefix=/usr/local/mapd-deps
make -j $(nproc)
sudo make install
popd

VERS=1.21-45
wget --continue https://github.com/jarro2783/bisonpp/archive/$VERS.tar.gz
tar xvf $VERS.tar.gz
pushd bisonpp-$VERS
./configure
make -j $(nproc)
sudo make install
popd
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get -y install cuda
git clone https://github.com/mapd/mapd-core
sudo mkdir ./mapd-core/build
sudo touch /etc/profile.d/mapd-deps.sh
sudo echo "LD_LIBRARY_PATH=/usr/local/cuda/lib64:\\$LD_LIBRARY_PATH" >>/etc/profile.d/mapd-deps.sh
sudo echo "LD_LIBRARY_PATH=/usr/lib/jvm/default-java/jre/lib/amd64/server:\\$LD_LIBRARY_PATH" >>/etc/profile.d/mapd-deps.sh
sudo echo "LD_LIBRARY_PATH=/usr/local/mapd-deps/lib:\\$LD_LIBRARY_PATH" >>/etc/profile.d/mapd-deps.sh
sudo echo "LD_LIBRARY_PATH=/usr/local/mapd-deps/lib64:\\$LD_LIBRARY_PATH" >>/etc/profile.d/mapd-deps.sh

sudo echo "PATH=/usr/local/cuda/bin:\\$PATH" >>/etc/profile.d/mapd-deps.sh
sudo echo "PATH=/usr/local/mapd-deps/bin:\\$PATH" >>/etc/profile.d/mapd-deps.sh

sudo echo "export LD_LIBRARY_PATH PATH" >>/etc/profile.d/mapd-deps.sh

LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
LD_LIBRARY_PATH=/usr/lib/jvm/default-java/jre/lib/amd64/server:$LD_LIBRARY_PATH
LD_LIBRARY_PATH=/usr/local/mapd-deps/lib:$LD_LIBRARY_PATH
LD_LIBRARY_PATH=/usr/local/mapd-deps/lib64:$LD_LIBRARY_PATH

PATH=/usr/local/cuda/bin:$PATH
PATH=/usr/local/mapd-deps/bin:$PATH

export LD_LIBRARY_PATH PATH

chown -R vagrant:vagrant ./mapd-core/
pushd mapd-core/build
cmake -DCMAKE_BUILD_TYPE=debug ..
make -j 4
   SHELL

end
