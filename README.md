### freeradius-poc
This is an Ansible Playbook to setup FreeRadius PoC for Cumulus Networks

### Requirements:

1. Cumulus switch running 3.7.12+
2. Debian Buster 10 VM install

You will be able to perform the following with this demo:

1. Wired 802.1X:

```https://docs.cumulusnetworks.com/cumulus-linux-37/Layer-1-and-Switch-Ports/802.1X-Interfaces/#configure-8021x-interfaces```

2. Wired MAC Authentication:

```https://docs.cumulusnetworks.com/cumulus-linux-37/Layer-1-and-Switch-Ports/802.1X-Interfaces/#configure-mac-authentication-bypass```

3. Wired 802.1X + MAC Authentication using MDA (Multi-Domain Authentication)

4. RADIUS AAA management of a switch:

```https://docs.cumulusnetworks.com/cumulus-linux-37/System-Configuration/Authentication-Authorization-and-Accounting/RADIUS-AAA/```

### Errata:

1. Configure MAC Authentication in the "authorized_macs" file located in:
  ```roles/freeradius/configurations ```

2. Configure the appropriate usernames at the following:
  ```roles/freeradius/authorize```
