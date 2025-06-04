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

Looks like this is the better more concistent way of addressing the ESA disk issue

https://williamlam.com/2025/02/vsan-esa-hardware-mock-vib-for-physical-esxi-deployment-for-vmware-cloud-foundation-vcf.html

Downloaded vib from here:

https://github.com/lamw/nested-vsan-esa-mock-hw-vib/releases

SCP vib to all ESXi servers (turn on ssh) 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ssh1.png)

```
scp nested-vsan-esa-mock-hw.vib root@192.168.1.51:/tmp/.
scp nested-vsan-esa-mock-hw.vib root@192.168.1.52:/tmp/.
scp nested-vsan-esa-mock-hw.vib root@192.168.1.53:/tmp/.
```

On each ESXi server run 

```
esxcli software acceptance set --level CommunitySupported
esxcli software vib install -v /tmp/nested-vsan-esa-mock-hw.vib --no-sig-check

/etc/init.d/vsanmgmtd restart

```
If needed the removal
```
esxcli software vib remove -n nested-vsan-esa-mock-hw
```
Changed my 2 Disks x # ESXi servers to NVMe controller (Created the controller first) disks (yes had to delete the disks and recreate them) 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/disk0.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/disk1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/disk2.png)

For good measure I restarted the vsan mgt

```
/etc/init.d/vsanmgmtd restart
```
Bingo - William Lam(s) stuff works !

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esa-works.png)

## Tracking down MTU issues

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/mtuissues1.png)


```
My Windows router with all interfaces
All running jumbo

netsh interface ipv4 show subinterfaces

   MTU  MediaSenseState   Bytes In  Bytes Out  Interface
------  ---------------  ---------  ---------  -------------
4294967295                1          0          0  Loopback Pseudo-Interface 1
  1500                1  191869171301  4133843676  Ethernet0
  9000                1       7686       1846  Ethernet1
  9000                1      21864       5466  Ethernet2
  9000                1      39867       8807  Ethernet3
  9000                1      88169      17999  Ethernet4
  9000                1      55453      11983  Ethernet5
  9000                1      70443      14883  Ethernet6

```

Checking from the ESXi server - not good 

```
vmkping -d  -s 1500  192.168.6.1
PING 192.168.6.1 (192.168.6.1): 1500 data bytes
sendto() failed (Message too long)
sendto() failed (Message too long)
sendto() failed (Message too long)

--- 192.168.6.1 ping statistics ---
3 packets transmitted, 0 packets received, 100% packet loss

```
Windows box is on all these networks 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/windowsnetworks1.png)

The 3 ESXi servers are on all of these networks

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esxinetworks1.png)

Making sure since this is nested that all my port groups have this set 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/makesure1.png)

Base switch shows me MTU 9000

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/makesure2.png)


Looks like I found part of the issue 
```
esxcfg-nics -l
Name    PCI          Driver      Link Speed      Duplex MAC Address       MTU    Description
vmnic0  0000:02:01.0 nvmxnet3    Up   10000Mbps  Full   00:50:56:9f:78:3b 9000   VMware Inc. vmxnet3 Virtual Ethernet Controller
vmnic1  0000:02:03.0 nvmxnet3    Up   10000Mbps  Full   00:50:56:9f:1c:73 1500   VMware Inc. vmxnet3 Virtual Ethernet Controller
vmnic2  0000:02:04.0 nvmxnet3    Up   10000Mbps  Full   00:50:56:9f:3c:f8 1500   VMware Inc. vmxnet3 Virtual Ethernet Controller
vmnic3  0000:02:05.0 nvmxnet3    Up   10000Mbps  Full   00:50:56:9f:e8:59 9000   VMware Inc. vmxnet3 Virtual Ethernet Controller
vmnic4  0000:02:06.0 nvmxnet3    Up   10000Mbps  Full   00:50:56:9f:8c:8a 9000   VMware Inc. vmxnet3 Virtual Ethernet Controller
vmnic5  0000:02:07.0 nvmxnet3    Up   10000Mbps  Full   00:50:56:9f:25:3f 9000   VMware Inc. vmxnet3 Virtual Ethernet Controller
```

```
esxcli network ip interface set -i vmk0 -m 9000
[root@vcf-esxi1:~] esxcli network ip interface list
```

