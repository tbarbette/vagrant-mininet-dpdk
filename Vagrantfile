$init = <<SCRIPT
  apt-get update
  DEBIAN_FRONTEND=noninteractive apt-get install -y build-essential fakeroot debhelper autoconf automake libssl-dev graphviz python-all libtool git tmux vim python3-pip python3-sphinx graphviz autoconf automake bzip2 debhelper dh-autoreconf libssl-dev libtool openssl procps python-all python-zopeinterface module-assistant dkms make libc6-dev python-argparse uuid-runtime netbase kmod  iproute2 openvswitch-switch libpcap-dev libnuma-dev libmicrohttpd-dev python3-pip python3-matplotlib htop wireshark-gtk python3 linux-tools-generic lynx gdb evince nginx libre2-dev openvswitch-switch
  pip install alabaster
  python3 -m pip install --upgrade pip
SCRIPT


$mininet = <<SCRIPT
  git clone http://github.com/mininet/mininet
  pushd mininet
  git checkout 2.3.1b1
  ./util/install.sh -fn
  popd
  echo "PATH=$PATH:/home/vagrant/mininet/util/" >> ~/.bashrc
SCRIPT

$dpdk = <<SCRIPT
  wget http://fast.dpdk.org/rel/dpdk-19.11.1.tar.xz
  tar xf dpdk-19.11.1.tar.xz
  rm dpdk-19.11.1.tar.xz
  pushd dpdk-stable-19.11.1
  make config T=x86_64-native-linuxapp-gcc O=x86_64-native-linuxapp-gcc
  sed -i 's#CONFIG_RTE_MACHINE="native"#CONFIG_RTE_MACHINE="default"#' x86_64-native-linuxapp-gcc/.config
  echo "export RTE_SDK=$(pwd)" >> ~/.profile
  echo "export RTE_TARGET=x86_64-native-linuxapp-gcc" >> ~/.profile
  cd x86_64-native-linuxapp-gcc
  make -j 4
  popd
SCRIPT

$huge = <<SCRIPT
  mkdir -p /mnt/huge
  (mount | grep hugetlbfs) > /dev/null || mount -t hugetlbfs nodev /mnt/huge
  echo "nodev /mnt/huge hugetlbfs       defaults        0 0" >> /etc/fstab
  echo 512 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages
  echo "vm.nr_hugepages=512" >> /etc/sysctl.conf
SCRIPT

$deps = <<SCRIPT
  python3 -m pip install --user pandas
  mkdir ~/.vimhistory/
  echo 'set undofile' >> .vimrc
  echo 'set undodir=/home/vagrant/.vimhistory/' >> .vimrc
  echo 'filetype plugin indent on' >> .vimrc
  echo 'set smarttab' >> .vimrc
  echo 'set shiftwidth=4' >> .vimrc
  echo 'set autowrite' >> .vimrc
  echo 'augroup filetypedetect' >> .vimrc
  echo '    au BufRead,BufNewFile *.click setfiletype click' >> .vimrc
  echo 'augroup END' >> .vimrc
  sudo locale-gen en_GB.UTF-8
  sudo service nginx stop
SCRIPT

$cleanup = <<SCRIPT
  apt-get clean
  rm -rf /tmp/*
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.provider "virtualbox" do |v|
	v.cpus = 3
	v.memory = 3072
  end

  config.vm.synced_folder "../../conf", "/conf"

  ## Provisioning
  config.vm.provision :shell, :inline => $init
  config.vm.provision :shell, privileged: false, :inline => $mininet
  config.vm.provision :shell, privileged: false, :inline => $dpdk
  config.vm.provision :shell, :inline => $huge
  config.vm.provision :shell, privileged: false, :inline => $deps
  config.vm.provision :shell, :inline => $cleanup

  config.ssh.forward_x11 = true

  config.vm.post_up_message = <<-HEREDOC
  Setup finished!
HEREDOC

end
