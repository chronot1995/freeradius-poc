### freeradius-poc

### WORK IN PROGRESS ###

### Description:

This is an Ansible Playbook to setup a FreeRadius server using Docker. This is used for a Cumulus Networks PoC on a Debian 10 VM or bare-metal server

### Configuration:



### Testing the above:

```
docker run -it --rm 2stacks/radtest radtest john testing123 localhost 0 testing123
```

### Errata:

The "old" directory contains the previous version of this repository that configured a VM / bare-metal Debian 10 Linux install as a PoC machine.
