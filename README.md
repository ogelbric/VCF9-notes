# VCF9 notes 

My Random notes on the install with the new VCF insaller (replces the spread sheet) 


```
ESXi Servers (3) are:
  24 CPU
  144 GB RAM
  1TB Disk
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ESXi1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ESXi2.png)

```
VCF installer
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install1.png)

```
How to get beyon this issue when the ESXi server is installed and upon boot get some strange DHCP address and you rename and re-IP the server.
```

```
Issue with two ESXi server (https://knowledge.broadcom.com/external/article/386968)
===================================================================
This Issue would occur if there is no matching FQDN for the hosts in the Certificate's subject alternative names and validate the vLAN ID's matching the deployment playbook. 


To re-generate ESXi host certificate to match subject alternate name (SAN) with ESXI host FQDN , refer the following steps. 

Log in to the ESXi Host Client.
Enable SSH on the ESXi host.
In the navigation pane, click Manage and click the Services tab.
Select the TSM-SSH service and click Start if not started.
Log in to the ESXi host using an SSH client such as Putty.
Regenerate the self-signed certificate by executing the following command
/sbin/generate-certificates

Reboot the ESXi to apply the changes
reboot

Verify the correct hostname is listed.
openssl x509 -in /etc/vmware/ssl/rui.crt -noout -text
X509v3 Subject Alternative Name should match the ESXi host FQDN
Repeat this procedure for all remaining hosts and restart the task on cloud builder.
```



![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install2.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install3.png)
