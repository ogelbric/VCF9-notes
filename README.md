# VCF9 notes 

My Random notes on the install with the new VCF insaller (replces the spread sheet) 

## DNS

```
DNS set up lab.local domain
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/DNS1.png)

## ESXi

```
ESXi Servers (3) are:
  24 CPU
  144 GB RAM
  1TB Disk
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ESXi1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ESXi2.png)

# VCF installer

```
Installer VM deployment
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install1.png)

```
Note!!! the installer is not the SDDC manager!
Hence the 192.168.1.23 address for the SDDC manager ....arrggg... :-(

```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install00.png)

```
The left and the right button can be done in parallel
In my case I am waiting for the token to down load
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install0.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install2.png)



```
How to get beyond this issue when the ESXi server is installed and
upon boot it gets some strange DHCP address and you rename and re-IP the server.
```

```
Issue with two ESXi server (https://knowledge.broadcom.com/external/article/386968)
===================================================================================
This Issue would occur if there is no matching FQDN for the hosts in the Certificate's
subject alternative names and validate the vLAN ID's matching the deployment playbook. 


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

Verify the correct hostname is listed (log back in Yes turn on ssh again!) .
openssl x509 -in /etc/vmware/ssl/rui.crt -noout -text
X509v3 Subject Alternative Name should match the ESXi host FQDN
Repeat this procedure for all remaining hosts and restart the task on cloud builder.
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install3.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/Install4.png)

## VCF install

```
Running the first test, but since I dont have the bits in the depot it stops
```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/InstallTest1.png)

## Notes on how to update the appliance for root login

```
Get onto the console of the appliance with set root password during install
Enable ssh
in the /etc/sshd/sshd_config file set PermitRootLogin yes
```
## Notes on how to point to STG depot

```
cp  /opt/vmware/vcf/lcm/lcm-app/conf/application-prod.properties  /opt/vmware/vcf/lcm/lcm-app/conf/application-prod.properties.bak.orf

vi /opt/vmware/vcf/lcm/lcm-app/conf/application-prod.properties

lcm.depot.adapter.host=dl-pstg.broadcom.com
lcm.depot.adapter.remote.vcfMetadataDir=/metadata-ob-24755599

systemctl restart {domainmanager.service,lcm.service}
```
## After that update the depot with the stg token/key (generatetoken.ps1)

```
Details I can not post here but send me an e-mail orf.gelbrich@broadcom.com

but it looks something like this:

Enter DepotTier (Production or Staging): Staging
Enter ClientId: 7Sxblablabal
Enter ClientSecret: PMySecretQaG
Enter UserEmail : my-e-mail@broadcom.com
[INFO] Staging tier download token: cMyTokenfor24hoursN


```

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/depot1.png)


![Version](https://github.com/ogelbric/VCF9-notes/blob/main/depot2.png)


![Version](https://github.com/ogelbric/VCF9-notes/blob/main/depot3.png)


![Version](https://github.com/ogelbric/VCF9-notes/blob/main/depot4.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/depot5.png)

## Fixing NTP on the 3 ESXi hosts

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ntp1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ntp2.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ntp3.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ntp4.png)

## ESA Disks on the ESXi servers (Data - 1000GB / Cache - 100GB) 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esa1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esa2.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esa3.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esa4.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esa5.png)

## ESA Cert disk needed 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esacert0.png)

Looks like the fix is documented here (I'll have to check it out):

https://williamlam.com/2023/11/custom-vsan-hcl-json-for-vmware-cloud-foundation-vcf-5-1-and-vsan-esa-using-nested-esxi.html#:~:text=Step%201%20%2D%20You%20will%20need,all.json)%20from%20VMware.

