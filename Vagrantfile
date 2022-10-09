Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"

  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    sudo apt-get update
    sudo apt-get upgrade -y
    sudo apt install -y python-is-python3
    sudo apt install -y python3-pip
    sudo apt-get install -y build-essential libffi-dev git pkg-config
    
    echo 'export "PATH=$HOME/.local/bin:$PATH"' >> ~/.bashrc
    
    mkdir ~/espressif
    cd ~/espressif
    wget https://github.com/jepler/esp-open-sdk/releases/download/2018-06-10/xtensa-lx106-elf-standalone.tar.gz
    tar zxvf xtensa-lx106-elf-standalone.tar.gz
    rm xtensa-lx106-elf/bin/esptool.py
    echo 'export "PATH=$HOME/espressif/xtensa-lx106-elf/bin/:$PATH"' >> ~/.bashrc
    
    source ~/.bashrc
    
    pip install esptool
    
    git clone https://github.com/micropython/micropython.git ~/micropython
    
    echo 'VM is ready for building'
  SHELL

  config.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.customize ["modifyvm", :id, "--usb", "on"]
  end

end
