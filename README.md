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
Setting hostname
```
esxcli system hostname set --host vcf-esxi2.lab.local
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

systemctl restart sshd

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
esxcli network ip interface list
vmk0
   Name: vmk0
   MAC Address: 00:50:56:9f:78:3b
   Enabled: true
   Portset: vSwitch0
   Portgroup: Management Network
   Netstack Instance: defaultTcpipStack
   VDS Name: N/A
   VDS UUID: N/A
   VDS Port: N/A
   VDS Connection: -1
   Opaque Network ID: N/A
   Opaque Network Type: N/A
   External ID: N/A
   MTU: 9000
   TSO MSS: 65535
   RXDispQueue Size: 4
   Port ID: 67108875
   URPF: 0

```

I set the modes on the ESXi servers as well 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esxihostaccept1.png)

## Overall Network 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/esxihostnaetworks1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/net1-6.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink0.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink1.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink2.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink3.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink4.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink5.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink6.png)

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/uplink61.png)

## All Green

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/allgreen1.png)

## vCenter Deploy error

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/vcenterdeploy1.png)

The log reveals: 

```
cd /var/log/vmware/vcf/domainmanager/ci-installer-04-06-25-19-58-699/workflow_1749067105841

cat vcsa-cli-installer.log
```


```
2025-06-04 19:59:34,815 - vCSACliInstallLogger - INFO - OVF Tool: Opening VI target: vi://vcf-esxi3.lab.local:443/
2025-06-04 19:59:36,845 - vCSACliInstallLogger - INFO - OVF Tool: Deploying to VI: vi://vcf-esxi3.lab.local:443/
2025-06-04 20:02:50,854 - vCSACliInstallLogger - INFO - OVF Tool: Transfer Completed
2025-06-04 20:02:53,057 - vCSACliInstallLogger - INFO - OVF Tool: Powering on VM: vcf-vcenter
2025-06-04 20:02:53,398 - vCSACliInstallLogger - INFO - OVF Tool: Task Failed
2025-06-04 20:02:53,415 - vCSACliInstallLogger - ERROR - Error: OVF Tool: Error: Task failed on server: This host does not support Intel VT-x.
2025-06-04 20:02:53,432 - vCSACliInstallLogger - INFO - OVF Tool: Error: Task failed on server: This host does not support Intel VT-x.
2025-06-04 20:02:55,379 - vCSACliInstallLogger - INFO - OVF Tool: Deleting VM: vcf-vcenter
2025-06-04 20:02:56,303 - vCSACliInstallLogger - ERROR - Error: OVF Tool: Completed with errors
2025-06-04 20:02:56,323 - vCSACliInstallLogger - INFO - OVF Tool: Completed with errors
2025-06-04 20:02:56,342 - vCSACliInstallLogger - ERROR - Deployment failed. OVF Tool return error code: 1
```

## Trying to overcome this error by enabeling hardware virtualisation

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/hardvirt1.png)

## vCenter is up and deployed and it loaded one ESXi host (not sure why not the other 2) 

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/oneesxihost1.png)

## SDDC manager get deployed but the deleted and re-deployed about 3-4 times 

```
SDDC Manager services did not start. Check logs for more details. Reference Token: FS5SKM
```

```
Error logs:
/var/log/vmware/vcf/commonsvcs/vcf-commonsvcs.log
/var/log/vmware/vcf/domainmanager/domainmanager.log
/var/log/vmware/vcf/operationsmanager/operationsmanager.log
/var/log/vmware/vcf/lcm/lcm.log

```
```
Have not found anything ... still looking
```
```
Looks like I have fallen into this scenario
Case-Sensitive Verification:
.
Some certificate verification processes, especially for SANs, can be case-sensitive.
```


SDDC manager trouble shooting
```
systemctl stop domainmanager
systemctl start domainmanager

tail -f /var/log/vmware/vcf/domainmanager/domainmanager.log

systemctl restart sddc-manager-ui-db
systemctl restart sddc-manager-ui-app

```

## Success 
```
DNS entry changed to external adress: 10.160.53.11
Made sure all switches were on MTU 9000 (Nested and not nested)
NTP running on all ESXi servers
2 partitions for vSAN
Secure boot disabled (settings on the VM(s)) for the ESXi servers (created strange errors for me in the neted env)
Snapshot'ed the ESXi servers with the VCF installer installed and all bianries downloaded fromd depot for faster cycle times

```
Sec boot

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/secboot.png)

VCF Installer

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/ins1.png)

Validate

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/s0.png)

Deployed
![Version](https://github.com/ogelbric/VCF9-notes/blob/main/s1.png)

Json file

```
{"sddcId":"vcf-mgt-dom1","workflowType":"VCF","hostSpecs":[{"hostname":"vcf-esxi1.lab.local","credentials":{"username":"root","password":""},"sslThumbprint":"33:3A:1F:B3:5C:D3:D7:F7:21:2C:B8:6F:D9:C0:BD:57:4E:8B:7F:31:6A:E7:9C:EA:97:79:E2:1A:FC:67:D1:D5"},{"hostname":"vcf-esxi2.lab.local","credentials":{"username":"root","password":""},"sslThumbprint":"EB:4F:52:43:F1:67:9E:FB:37:19:3F:02:36:9D:53:8B:1F:F8:E4:15:9C:5E:51:2D:9F:03:C8:BD:EA:20:B0:74"},{"hostname":"vcf-esxi3.lab.local","credentials":{"username":"root","password":""},"sslThumbprint":"33:32:7C:88:C7:72:14:CD:D8:32:20:03:EE:76:4D:B8:8D:D9:81:20:46:26:B4:95:A1:EC:8B:E1:0B:2D:8C:A4"}],"version":"9.0.0.0","vcenterSpec":{"vcenterHostname":"vcf-vcenter.lab.local","rootVcenterPassword":"","vmSize":"small","ssoDomain":"vsphere.local","adminUserSsoPassword":"","useExistingDeployment":false},"clusterSpec":{"datacenterName":"vcf-mgt-dom1-dc01","clusterName":"vcf-mgt-dom1-cl01"},"dvsSpecs":[{"dvsName":"vcf-mgt-dom1-cl01-vds01","networks":["MANAGEMENT","VM_MANAGEMENT","VMOTION","VSAN"],"mtu":9000,"nsxtSwitchConfig":{"transportZones":[{"name":"VCF-Created-Overlay-Zone","transportType":"OVERLAY"}]},"vmnicsToUplinks":[{"id":"vmnic0","uplink":"uplink1"},{"id":"vmnic1","uplink":"uplink2"}],"nsxTeamings":[{"policy":"LOADBALANCE_SRCID","activeUplinks":["uplink1","uplink2"]}]}],"nsxtSpec":{"nsxtManagers":[{"hostname":"vcf-nsx1.lab.local"}],"nsxtManagerSize":"large","vipFqdn":"vcf-nsx-vip.lab.local","rootNsxtManagerPassword":"","nsxtAdminPassword":"","nsxtAuditPassword":"","transportVlanId":0,"ipAddressPoolSpec":{"name":"TZ-IP-Pool","description":"TZ-IP-Pool-Description","subnets":[{"ipAddressPoolRanges":[{"start":"192.168.6.100","end":"192.168.6.200"}],"cidr":"192.168.6.0/24","gateway":"192.168.6.1"}]},"skipNsxOverlayOverManagementNetwork":true,"useExistingDeployment":false},"networkSpecs":[{"networkType":"MANAGEMENT","subnet":"192.168.1.0/24","gateway":"192.168.1.1","vlanId":0,"mtu":9000,"teamingPolicy":"loadbalance_loadbased","activeUplinks":["uplink1","uplink2"],"portGroupKey":"vcf-mgt-dom1-cl01-vds01-pg-esx-mgmt"},{"networkType":"VM_MANAGEMENT","subnet":"192.168.1.0/24","gateway":"192.168.1.1","vlanId":0,"mtu":9000,"teamingPolicy":"loadbalance_loadbased","activeUplinks":["uplink1","uplink2"],"portGroupKey":"vcf-mgt-dom1-cl01-vds01-pg-vm-mgmt"},{"networkType":"VMOTION","subnet":"192.168.4.0/24","gateway":"192.168.4.1","includeIpAddressRanges":[{"startIpAddress":"192.168.4.100","endIpAddress":"192.168.4.200"}],"vlanId":0,"mtu":9000,"teamingPolicy":"loadbalance_loadbased","activeUplinks":["uplink1","uplink2"],"portGroupKey":"vcf-mgt-dom1-cl01-vds01-pg-vmotion"},{"networkType":"VSAN","subnet":"192.168.5.0/24","gateway":"192.168.5.1","includeIpAddressRanges":[{"startIpAddress":"192.168.5.100","endIpAddress":"192.168.5.200"}],"vlanId":0,"mtu":9000,"teamingPolicy":"loadbalance_loadbased","activeUplinks":["uplink1","uplink2"],"portGroupKey":"vcf-mgt-dom1-cl01-vds01-pg-vsan"}],"dnsSpec":{"subdomain":"lab.local","nameservers":["10.160.53.11"]},"ntpServers":["ntp.broadcom.net"],"sddcManagerSpec":{"rootPassword":"","hostname":"vcf-installer.lab.local","sshPassword":"","localUserPassword":"","useExistingDeployment":true},"managementPoolName":"vcf-mgt-dom1-np01","ceipEnabled":false,"datastoreSpec":{"vsanSpec":{"datastoreName":"vcf-mgt-dom1-cl01-ds-vsan01","esaConfig":{"enabled":true}}},"vcfOperationsFleetManagementSpec":{"hostname":"vcf-fleetmgt.lab.local","rootUserPassword":"","adminUserPassword":"","useExistingDeployment":false},"vcfOperationsSpec":{"nodes":[{"hostname":"vcf-operations1.lab.local","rootUserPassword":"","type":"master"}],"adminUserPassword":"","applianceSize":"small","useExistingDeployment":false},"vcfOperationsCollectorSpec":{"hostname":"vcf-operations4.lab.local","rootUserPassword":"","applianceSize":"small","useExistingDeployment":false},"vcfAutomationSpec":{"hostname":"vcf-automation-vip.lab.local","adminUserPassword":"","ipPool":["192.168.1.31","192.168.1.32"],"internalClusterCidr":"198.18.0.0/15","nodePrefix":"autoprefix1","useExistingDeployment":false},"vcfInstanceName":"vcfinstname1"}
```

# VCF CLI commands 

vcf context create --endpoint=192.168.2.220 --insecure-skip-tls-verify
or
vcf context create --endpoint=192.168.2.220 --insecure-skip-tls-verify --auth-type basic

```
? Provide a name for the context:  k1
[i] Auth type vSphere SSO detected. Proceeding for authentication...
? Provide Username:  administrator@vsphere.local

? Provide Username:  administrator@vsphere.local
Provide Password:

Logged in successfully.

You have access to the following contexts:
   k1
   k1:namespace1000
   k1:svc-tkg-domain-c10
   k1:svc-velero-domain-c10

If the namespace context you wish to use is not in this list, you may need to
refresh the context again, or contact your cluster administrator.

To change context, use `vcf context use <context_name>`
[ok] successfully created context: k1
[ok] successfully created context: k1:svc-tkg-domain-c10
[ok] successfully created context: k1:svc-velero-domain-c10
[ok] successfully created context: k1:namespace1000

```

vcf context list

```
  NAME                      CURRENT  TYPE
  k1                        false    kubernetes
  k1:namespace1000          false    kubernetes
  k1:svc-tkg-domain-c10     false    kubernetes
  k1:svc-velero-domain-c10  false    kubernetes
```

vcf context use k1

```
[ok] Token is still active. Skipped the token refresh for context "k1"
[i] Successfully activated context 'k1' (Type: kubernetes)
[i] Fetching recommended plugins for active context 'k1'...
[i] Installing the following plugins recommended by context 'k1':
  NAME                INSTALLING
  cluster             v3.3.1
  kubernetes-release  v3.3.1
  namespaces          v9.0.0
  package             v3.3.1
  registry-secret     v3.3.1
  vm                  v9.0.0
```

Load Cluster plugin

```
vcf plugin install cluster
```

delete a cluster

```
vcf cluster delete cluster3 -n namespace1000
```


# Cluster yaml for vCenter 9 

```
apiVersion: cluster.x-k8s.io/v1beta1
kind: Cluster
metadata:
  name: cluster4
  namespace: namespace1000
spec:
  clusterNetwork:
    services:
      cidrBlocks: ["10.96.0.0/12"]
      # cidrBlocks: ["192.168.0.0/20"]
    pods:
      cidrBlocks: ["192.168.16.0/20"]
      # cidrBlocks: ["192.168.16.0/20"]
    serviceDomain: "cluster.local"
  topology:
    #class: tanzukubernetescluster
    class: builtin-generic-v3.1.0
    version: v1.32.0---vmware.6-fips-vkr.2
    controlPlane:
      replicas: 1
    workers:
      machineDeployments:
        - class: node-pool
          name: node-pool-1
          replicas: 1
    variables:
      - name:  ntp
        value: "ntp.broadcom.net"
      - name: vmClass
        value: best-effort-small
      - name: storageClass
        value: management-storage-policy-regular
      - name: defaultStorageClass
        value: management-storage-policy-regular
      - name: nodePoolVolumes
        value:
        - capacity:
            storage: "40Gi"
          mountPath: "/var/lib/containerd"
          name: containerd
          storageClass: management-storage-policy-regular
      - name: controlPlaneVolumes
        value:
        - capacity:
            storage: "4Gi"
          mountPath: "/var/lib/etcd"
          name: etcd
          storageClass: management-storage-policy-regular
```

More VCF commands
```
vcf context  use k1    (Works as login as well) 

vcf cluster create  cluster3 -n namespace1000 -f ./downloads/cluster3.yaml
 
vcf cluster list -A
  NAME      NAMESPACE      STATUS    CONTROLPLANE  WORKERS  KUBERNETES             KUBERNETESRELEASE
  cluster3  namespace1000  creating  0/1           0/1      v1.32.0+vmware.6-fips  v1.32.0---vmware.6-fips-vkr.2
```

The kubectl commands work as well
```
kubectl apply -f ./downloads/cluster3.yaml -n namespace1000
cluster.cluster.x-k8s.io/cluster3 created

kubectl config use-context k1:namespace1000

kubectl vsphere login --server 192.168.2.220 --vsphere-username administrator@vsphere.local --tanzu-kubernetes-cluster-namespace namespace1000 --tanzu-kubernetes-cluster-name cluster3 --insecure-skip-tls-verify
```

The same workload cluster login with VCF commands 

```
vcf context create --endpoint 192.168.2.220 --username administrator@vsphere.local --workload-cluster-name cluster3 --workload-cluster-namespace namespace1000 --insecure-skip-tls-verify
```
```
? Provide a name for the context:  k2

? Provide a name for the context:  k2
Provide Password:



[i] Logging in to Kubernetes cluster (cluster3) (namespace1000)
[i] Successfully logged in to Kubernetes cluster 192.168.2.223

You have access to the following contexts:
   k2
   k2:cluster3

If the namespace context you wish to use is not in this list, you may need to
refresh the context again, or contact your cluster administrator.

To change context, use `vcf context use <context_name>`
[ok] successfully created context: k2
[ok] successfully created context: k2:cluster3
```
```
vcf context use k2:cluster3
```
```
kubectl get nodes
NAME                                     STATUS   ROLES           AGE   VERSION
cluster3-k9n6j-r8ssz                     Ready    control-plane   15m   v1.32.0+vmware.6-fips
cluster3-node-pool-1-jpt9c-r4knr-jrb85   Ready    <none>          11m   v1.32.0+vmware.6-fips

```

Context for cluster refresh
```
vcf contexts refresh --insecure-skip-tls-verify
```



# VCF-A

Provider Interface
```
https://vcf-automation-vip.lab.local/provider
```
User Interface
```
http://vcf-automation-vip.lab.local
```



# Notes
```

kubectl vsphere login --server 192.168.2.220 --vsphere-username administrator@vsphere.local --insecure-skip-tls-verify

kubectl vsphere login --server 192.168.2.220 --vsphere-username administrator@vsphere.local --tanzu-kubernetes-cluster-namespace namespace1000 --tanzu-kubernetes-cluster-name cluster3 --insecure-skip-tls-verify

```

