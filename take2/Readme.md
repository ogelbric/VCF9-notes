# Off Line install VCF stack (take 2) 

## Moving the downloaded (supposrt.broadcom.com) software into the correct folder structure 
```
sudo su
mv /bigdisk/orf/VMware-VMvisor-Installer-9.0.1.0.24957456.x86_64.iso /bigdisk/VCF9/PROD/COMP/ESX_HOST/.
mv /bigdisk/orf/VMware-VCSA-all-9.0.1.0.24957454.iso /bigdisk/VCF9/PROD/COMP/VCENTER/.
mv /bigdisk/orf/VCF-SDDC-Manager-Appliance-9.0.1.0.24962180.ova /bigdisk/VCF9/PROD/COMP/SDDC_MANAGER_VCF/.
mv /bigdisk/orf/nsx-unified-appliance-9.0.1.0.24952114.ova /bigdisk/VCF9/PROD/COMP/NSXT_MANAGER/.
mv /bigdisk/orf/vmsp-vcfa-combined-9.0.1.0.24965341.tar /bigdisk/VCF9/PROD/COMP/VRA/.
mv /bigdisk/orf/Operations-Appliance-9.0.1.0.24960351.ova /bigdisk/VCF9/PROD/COMP/VROPS/.


mv /bigdisk/orf/VCF-OPS-Lifecycle-Manager-Appliance-9.0.1.0.24960371.ova /bigdisk/VCF9/PROD/COMP/VRSLCM/VCF-OPS-Lifecycle-Manager-Appliance-9.0.1.0.24960371.ova
mkdir /bigdisk/VCF9/PROD/COMP/VCF_OPS_CLOUD_PROXY
mv /bigdisk/orf/Operations-Cloud-Proxy-9.0.1.0.24960349.ova /bigdisk/VCF9/PROD/COMP/VCF_OPS_CLOUD_PROXY/Operations-Cloud-Proxy-9.0.1.0.24960349.ova

mkdir /bigdisk/VCF9/PROD/COMP/NSX_T_MANAGER
mv /bigdisk/VCF9/PROD/COMP/NSXT_MANAGER/nsx-unified-appliance-9.0.1.0.24952114.ova /bigdisk/VCF9/PROD/COMP/NSX_T_MANAGER/nsx-unified-appliance-9.0.1.0.24952114.ova

```


## Cert for SDDC manager from jump box

```

Jump to SDDC Installer VM Console and enable ssh root login
vi /etc/ssh/sshd_config
Change this line: PermitRootLogin yes
systemctl restart sshd

#https://vmw-confluence.broadcom.net/pages/viewpage.action?pageId=2233408098&spaceKey=~dzorgnotti&title=VCF%2B9%2B-%2BOffline%2BDepot%2Bwith%2BEnterprise%2BCA
#on jump host
cp cert.crt enterprise-ca.pem

#Move cert to SDDC manager
scp enterprise-ca.pem root@192.168.1.23:/etc/ssl/certs/.
#ssh to sddc manager
ssh root@192.168.1.23

/usr/bin/rehash_ca_certificates.sh

keytool -import -trustcacerts -file /etc/ssl/certs/enterprise-ca.pem -keystore /usr/lib/jvm/openjdk-java17-headless.x86_64/lib/security/cacerts -alias enterprise-ca
#The password is "changeit"

Restart the LCM app
service lcm restart
```

## Log into the jumphost https repo

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/take2/offline_depot.png)



### Take 2
