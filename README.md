### freeradius-poc

### Description:

This container-based demo has been rewritten to use Alpine Linux for a FreeRadius PoC with Cumulus Linux. The FreeRadius PoC will be able to provide RADIUS authentication to a Cumulus switch, wired 802.1x access to a Cumulus Switch, and MAC authentication to a Cumulus Switch. This has been verified with Cumulus Linux 4.2.1. It has been instantiated on a Debian Buster server running Docker and macOS running Docker Desktop.

### Configuration:

1. The first step in this demo is to setup Docker on the appropriate server. There is an Ansible playbook that installs the Docker components with the following commands:

```
git clone https://gitlab.com/nvidia-networking/systems-engineering/poc-support/freeradius-poc
```

Change directories into the PoC directory:

```
cd freeradius-poc
```

In this directory is a sample "hosts" file. Make the appropriate changes to the username, password, and IP address for your server. Once that is complete, run the playbook with the following command:

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

This will build the Alpine Linux with FreeRadius container and will place the configuration files into the appropriate locations.

3. Verify connectivity

FreeRadius will be configured in a verbose debug mode. To view the output from FreeRadius, run the following command:

```
sudo docker logs freeradius-poc
```

4. Stop the FreeRadius PoC

To stop the PoC run the following command:

```
sudo docker-compose stop
```

### Remove the Docker container

In order to make any changes to the default configuration outlined in the below steps, you will need to delete and rebuild the container and the associated image.

1. First, remove the FreeRadius PoC container using the following command:

```
sudo docker rm freeradius-poc
```

2. Remove the container image using the following command:

```
sudo docker image rm nvidia/radius
```

### Adding a new switch to FreeRadius

First, one needs to add the a new RADIUS "client" or NAD to FreeRadius. Go to the following directory:

```
cd freeradius-poc/docker/config
```

The "clients.conf" file is where the NADs are defined. In the following example, we are defining a single switch with a /32 mask:

```
client cumulus-switch {
	ipaddr		= 192.168.255.100/32
	secret		= cumulus11
}
```

One can use larger subnets for ranges of IP addresses, as well. These NAD entries must be configured before FreeRadius will respond to a request.

### Testing RADIUS Authentication to a Cumulus Switch

1. Follow the directions here to configure RADIUS AAA authentication on a Cumulus Switch:

https://docs.cumulusnetworks.com/cumulus-linux-42/System-Configuration/Authentication-Authorization-and-Accounting/RADIUS-AAA/

2. The following is authentication information for a defined user:

Username: testinguser
Password: cumulus11

### Testing Wired 802.1x Authentication to a Cumulus Switch

1. Follow the directions here to configure Wired 802.1x Authentication to a Cumulus Switch:

https://docs.cumulusnetworks.com/cumulus-linux-42/Layer-1-and-Switch-Ports/802.1X-Interfaces/#configure-8021x-interfaces

2. The usernames and passwords are stored in the following directory:

```
cd freeradius-poc/docker/config
```
in the "authorize" file

3. Here is an example entry from this file:

```
testinguser Cleartext-Password := "cumulus11"
		Tunnel-Type = "VLAN",
		Tunnel-Medium-Type = "IEEE-802",
		Tunnel-Private-Group-Id = "17",
		Service-Type = NAS-Prompt-User,
    Cisco-AVPair = "shell:priv-lvl=15"
```

The above has a couple of important pieces. First, the last line, "Cisco-AVPair = "shell:priv-lvl=15"" defines the RADIUS AAA Authentication to the switch. The other fields are important to pass VLAN "17" to your Cumulus Switch. This RADIUS VSA (Vendor Specific Attribute) should be tailored to your environment if you would like to demonstrate VLAN the Dynamic VLAN functionality

### Testing Wired MAC Authentication to a Cumulus Switch

1. Follow the directions here to configure Wired MAC Authentication to a Cumulus Switch:

https://docs.cumulusnetworks.com/cumulus-linux-42/Layer-1-and-Switch-Ports/802.1X-Interfaces/#configure-mac-authentication-bypass

2. The MAC Addresses are stored in the following directory:

```
cd freeradius-poc/docker/config
```
in the "authorized_macs" file

3. Here is an example entry from this file:

```
A0-09-ED-0D-83-CC
    Cleartext-Password := "A0-09-ED-0D-83-CC",
    User-Name := "A0-09-ED-0D-83-CC",
    Service-Type = Framed-User,
    Tunnel-Type = VLAN,
    Tunnel-Medium-Type = 6,
#    Tunnel-Private-Group-id = 16,
    Cisco-AVPair = "device-traffic-class=voice",
    Reply-Message = "Device %{Calling-Station-ID} authorized"
```

The username and cleartext password must match for MAC Authentication to occur. The above example also sets the VLAN to the Voice VLAN as it defined on the local switch; it does not have to be specifically passed as a RADIUS VSA. The "Tunnel-Private-Group-id" entry is commented out here as a reminder that it is not required.

### Useful Container Commands:

Here are some useful Docker commands specific to this

```
sudo docker build -t nvidia/radius .
sudo docker run -p 1812-1813:1812-1813 --name=freeradius-poc -d nvidia/radius
sudo docker exec -it freeradius-poc /bin/bash
```

These are some additional Docker commands that are useful to know:

```
sudo docker ps -a
sudo docker ps | grep -i radius | awk -F " " '{print $11}'
sudo docker exec -it $(sudo docker ps | grep -i radius | awk -F " " '{print $13}') /bin/bash
sudo docker exec -it <container name> /bin/bash
sudo docker container stats
sudo docker rm <container name>
sudo docker image rm <image name>
sudo docker inspect <container name>
sudo docker logs <container name>
```

### Useful Authenticaiton Tools:

Within the container:
```
radtest testinguser cumulus11 localhost 0 testing123
```

EAPTest for the Mac:

http://www.ermitacode.com/eaptest.html

### Errata:

The "old" directory contains the previous version of this repository that used Ansible to configure a VM / bare-metal Debian 10 Linux install as a Freeradius PoC machine. I was able to reduce the size of the image from a full VM down to 20mb in the current incarnation.
