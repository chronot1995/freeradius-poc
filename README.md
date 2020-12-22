### freeradius-poc

### Description:

This container-based demo has been rewritten to use Alpine Linux for a FreeRadius PoC with Cumulus Linux. This has been verified on a Debian Buster server running Docker and macOS running Docker Desktop.

### Configuration:

1. The first step in this demo is to setup Docker on the appropriate server. There is an Ansible playbook that installs the Docker components

```
git clone https://gitlab.com/nvidia-networking/systems-engineering/poc-support/freeradius-poc
```

```
cd freeradius-poc
```

In this directory is a sample "hosts" file. Make the appropriate changes to your username, password, and IP address for your server. Once that is complete, run the playbook with the following command:

```
ansible-playbook freeradius.yml
```

2. After Docker is installed, the next step is to build and run the FreeRadius container. Switch directories to the Docker directory:

```
cd docker
```
run the following command:

```
sudo docker-compose up -d
```

This will build and 

### Authentication

Username: testinguser
Password: cumulus11
Shared Secret: cumulus11

### Useful Container Commands:

sudo docker build -t nvidia/radius .

sudo docker run -p 1812-1813:1812-1813 --name=freeradius-poc -d nvidia/radius

sudo docker ps -a
sudo docker rm freeradius-poc
sudo docker ps | grep -i radius | awk -F " " '{print $11}'
sudo docker exec -it $(sudo docker ps | grep -i radius | awk -F " " '{print $13}') /bin/bash

sudo docker exec -it freeradius-poc /bin/bash

sudo docker-compose stop
sudo docker exec -it <container name> /bin/bash
sudo docker ps
sudo docker container stats
sudo docker image rm <container ID>


### Useful Authenticaiton Tools:

Within the container:
```
radtest testinguser cumulus11 localhost 0 testing123
```

EAPTest for the Mac:

http://www.ermitacode.com/eaptest.html

### Errata:

The "old" directory contains the previous version of this repository that used Ansible to configure a VM / bare-metal Debian 10 Linux install as a Freeradius PoC machine. I was able to reduce the size of the image from a full VM down to 20mb in the current incarnation.
