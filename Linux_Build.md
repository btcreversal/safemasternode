# Linux Build Instructions
safemasternode requires
* openssl-1.0.2n
* berkeley 4.8 db
* boost 1.57
* miniupnpc

To install, first follow the instructions to install dependencies 18.04 then follow the compilation instructions.

  ## Dependencies (Ubuntu 18.04)
    sudo -i
    sudo apt-get update && apt-get upgrade  
    sudo apt-get dist-upgrade
    sudo apt-get install git screen net-tools -y
    sudo apt-get install libzmq3-dev libminiupnpc-dev libevent-dev -y  
    sudo apt-get install build-essential libtool autotools-dev automake pkg-config -y  
    sudo apt-get install libevent-dev bsdmainutils software-properties-common -y  
    sudo apt-get install libboost-all-dev -y  
    sudo add-apt-repository ppa:safemasternode/safemasternode  
    sudo apt-get update
    sudo apt-get install libdb4.8-dev libdb4.8++-dev  -y  
    sudo apt-get install libgmp3-dev -y
    sudo apt-get install libdb5.3++ unzip libzmq5 -y
    sudo apt-get install libminiupnpc-dev -y
    
    ##### Special instructions for openssl-1.0.2n on Ubuntu 18.04:
    cd ~
    wget http://www.openssl.org/source/openssl-1.0.2n.tar.gz
    tar -xvzf openssl-1.0.2n.tar.gz
    cd openssl-1.0.2n
    ./config --prefix=/usr/
    make
    sudo make install
    
    openssl version
      
#### Swapfile
    swapoff -a
    fallocate -l 4G /swapfile  
    chown root:root /swapfile  
    chmod 0600 /swapfile  
    sudo bash -c "echo 'vm.swappiness = 10' >> /etc/sysctl.conf"  
    mkswap /swapfile  
    swapon /swapfile    
    echo '/swapfile none swap sw 0 0' >> /etc/fstab
    
    free -m 
    df -h

#####  Restart the system
    sudo reboot

#####  Download Source code
    cd ~
    sudo git clone https://github.com/safeminecoin/safemasternode.git

##### Install libdb6.2 (Berkeley DB)
    cd safemasternode
    safemasternode_ROOT=$(pwd)
    BDB_PREFIX="${safemasternode_ROOT}/build"
    mkdir -p $BDB_PREFIX
    wget 'http://download.oracle.com/berkeley-db/db-6.2.32.tar.gz'
    tar -xzvf db-6.2.32.tar.gz
    cd db-6.2.32/build_unix/
    ../dist/configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_PREFIX --without-gui --enable-upnp-default
    make install

### Compiling  
    cd safemasternode/src
    sudo make -f makefile.unix
    strip safemasternoded
    
    example :
    sudo make -f makefile.unix USE_UPNP=1 BDB_INCLUDE_PATH='/usr/include/db4.8 

##### Edit the config file  
    
    sudo ufw allow 32821/tcp
    sudo ufw allow 32822/tcp
    sudo ufw allow 22/tcp

    sudo nano /root/.safemasternode/safemasternode.conf

		rpcuser=username(Configure your own)  
		rpcpassword=password(Configure your own)  
		rpcallowip=127.0.0.1  
		rpcport=32821
		port=32822
		daemon=1  
		server=1  
		listen=1  
		logtimestamps=1  
		maxconnections=256  

	For masternodes, also add:

		masternode=1  
		masternodeprivkey=your private key
		masternodeaddr=your VPS IP:22022

#### Usage  
Start daemon

	./safemasternoded
	systemctl start safemasternode.service

Stop daemon

	./safemasternoded stop
	systemctl stop safemasternode.service

Display information  

	./safemasternoded help
	./safemasternoded getinfo  
	./safemasternoded getmininginfo  
	./safemasternoded getblockcount  
	./safemasternoded masternode status  
	./safemasternoded masternode list  
___
