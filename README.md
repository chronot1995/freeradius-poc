### freeradius-poc

### WORK IN PROGRESS ###

### Description:

This container-based radius demo has been rewritten to use Alpine Linux with Docker as a FreeRadius PoC for Cumulus Linux

### Configuration:

git clone
cd freeradius-poc/docker
sudo docker-compose up -d

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
