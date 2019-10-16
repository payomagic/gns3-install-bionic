# gns3-install-bionic
 Installing GNS3-Server on Ubuntu 18.04 LTS (Bionic Beaver)

# Step 1: Update your distro and install needed packages

```
sudo apt-get update
sudo apt upgrade
sudo apt-get install build-essential git libpthread-stubs0-dev libpcap-dev qemu-kvm libvirt-bin virtinst bridge-utils cpu-checker cmake libelf-dev
sudo apt-get install python3-setuptools python3-aiohttp python3-psutil python3-jsonschema
```

# Step 2: Install ubridge, dynamips, docker, and gns3 from source
## Install ubridge

```
cd ~
git clone https://github.com/GNS3/ubridge.git
cd ubridge
make 
sudo make install
```

## Install dynamips

```
cd ~
git clone git://github.com/GNS3/dynamips.git
cd dynamips
mkdir build
cd build
cmake .. -DDYNAMIPS_CODE=stable
sudo make install
```

## Install gns3-server

```
cd ~
git clone https://github.com/GNS3/gns3-server.git
cd gns3-server/
sudo python3 setup.py install
```

## Install Docker

```
cd ~
sudo apt install docker.io
sudo systemctl enable docker
```

# Step 3: Create user, group membership, & startup script
## Create the gns3 user and add it to the needed groups

```
sudo adduser gns3
sudo adduser gns3 kvm
sudo adduser gns3 docker
```

Copy the gns3 startup template from the source tree to the init directory
```
sudo cp ~/gns3-server/init/gns3.service.systemd /lib/systemd/system/gns3.service
```

I usually reboot here, just to be sure I have the correct permissions and everything is ready to go.

# Step 3.5: (Optional) Install a few useful Docker containers

```
sudo docker pull gns3/kalilinux
sudo docker pull gns3/endhost
```

# Step 4: Startup and enable the gns3 server

Enable the gns3 service by default
```
sudo systemctl enable gns3
```
Manually start the gns3 service
```
sudo systemctl restart gns3
```
You can verify the server started or troubleshoot any issues by looking the journal logs.
```
sudo journalctl -xe
Jun 19 20:31:34 delores systemd[1]: Starting GNS3 server...
-- Subject: Unit gns3.service has begun start-up
-- Defined-By: systemd
-- Support: http://www.ubuntu.com/support
--
-- Unit gns3.service has begun starting up.
Jun 19 20:31:34 delores systemd[1]: gns3.service: Can't open PID file /var/run/gns3/gns3.pid (yet?) after start: No such file or directory
Jun 19 20:31:36 delores gns3server[8140]: No configuration file could be found or read
Jun 19 20:31:36 delores systemd[1]: Started GNS3 server.
-- Subject: Unit gns3.service has finished start-up
-- Defined-By: systemd
-- Support: http://www.ubuntu.com/support
--
-- Unit gns3.service has finished starting up.
--
-- The start-up result is RESULT.
```
And by looking for the running binary.
```
ps -ef | grep gns3
gns3 8150 1 3 20:31 ? 00:00:01 /usr/bin/python3 /usr/local/bin/gns3server --log /var/log/gns3/gns3.log --pid /var/run/gns3/gns3.pid --daemon
```
# Step 5: Connect with the gns3 gui client

If someone already have a GNS3 client installed somewhere, and just want to make one more GNS3 environment as my case, beware that your client version vs the gns-server you are building from “https://github.com/GNS3/gns3-server.git”. They need to of the same version. 
