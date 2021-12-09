# Setup Fantom blockchain read-only node

This document contains instructions for setting up a Fantom Read-only node on the Fantom mainnet.
You can find the offical docs for the current version here <a href="https://github.com/Fantom-foundation/lachesis_launch">Fantom Lachesis</a> 

These instructions have been developed to configure a Fantom Read-only node using Ubuntu 20.04 LTS on VM with 2TB SSD and 24GB RAM. These instructions are primarily for my own purposes, so that I can recreate my environment if I need to. They are not intended to represent best practices and may not be applicable to your hardware, software, or network configuration. There are many other good sources for instructions on setting up these services, and those may be more generally written and applicable.

Setup includes installation and configuration of the following services, including setting up systemd to automatically run services, where applicable:

  - Ubuntu Prerequisities
  - General Ubuntu Security and Monitoring
  - Go-Opera Read-only node

## Prerequisities

### Software Update

After an initial install, it is a good idea to update everything to the latest versions.

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get autoremove
sudo reboot
```

### Set Time Zone

Run the following command to see the list of time zones, then copy the appropriate time zone to your clipboard.

```
timedatectl list-timezones
```

### Install net-tools

Installing net-tools in order to determine network device via ifconfig.

```
sudo apt-get install net-tools
```

### Install make

For builds

```
sudo apt-get install make
```

## General Security

### Install Fail2ban

Fail2ban is a daemon that monitors login attempts to a server and blocks suspicious activity as it occurs. It’s well configured out of the box.

```
sudo apt-get install fail2ban
```

### Lock down ssh

```
sudo vi /etc/ssh/sshd_config
```

Add the following lines, but replacing with your login. You are not logging in to ssh with root, right? If you are, you probably don't want to add the AllowUsers and PermitRootLogin lines below.

**Opitonal**: You can also set @IP for the users you wish to permit

```
AllowUsers <LOGIN>
PasswordAuthentication no
PermitEmptyPasswords no
PermitRootLogin no
Protocol 2
```

**Optional**: I prefer to change the default SSH port to a non-standard port. Do not forget what you change this to. Find the following line, uncomment it line by removing the "#", and replace "22" with your preferred port.

```
#Port 22
```

Restart the service and reboot to confirm

```
sudo systemctl restart ssh
sudo reboot
```

### Firewall

TODO: Still unsure of all the ports I want open till I setup the node API


## Install Read-only node

### Create a user account

```
sudo adduser --home /home/opera --disabled-password --gecos 'Fantom Opera Client' opera

```

### Install golang

```
wget https://dl.google.com/go/go1.15.10.linux-amd64.tar.gz
sudo tar -xvf go1.15.10.linux-amd64.tar.gz -C /usr/local/
```

### Setup goland environment variables in the .bashrc

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```
Dont forget to .source the .bashrc after use

### Checkout and build go-opera

```
git clone https://github.com/Fantom-foundation/go-opera.git
cd go-opera/
git checkout release/1.0.2-rc.5
make
```

### Download the genesis file
This will take a couple mins

```
cd build/
wget https://opera.fantom.network/mainnet.g
```

### Setup a systemd service file

```
sudo vi /etc/systemd/system/opera.service
```

Copy the following

```
[Unit]
Description=Fantom Go-Opera Client
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=5
User=opera
WorkingDirectory=/home/opera/go-opera/build
ExecStart=/home/opera/go-opera/build/opera --genesis mainnet.g

[Install]
WantedBy=multi-user.target
```

### Start Go-Opera

```
sudo systemctl daemon-reload
sudo systemctl start opera
sudo systemctl enable opera
```

### View logs to confirm it is working correctly

```
sudo journalctl -u opera -f
```
