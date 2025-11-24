# Off Line install VCF stack (take 2) 

## Jump host needs a bigger disk to hold all images

```
Add a disk to your linux Jump host in vCenter !!  (re-boot vm?)

#Disk shows up
lsblk
#sdb                         8:16   0     2T  0 disk
#
# looks like sdb      8:16   0  150G  0 disk  is my new disk 
	sudo parted /dev/sdb
	mklabel gpt
	print
	mkpart primary 0 1995GB
	Ignore
	quit
	sudo mkfs.ext4 /dev/sdb1
	sudo mkdir /bigdisk
	sudo mount /dev/sdb1 /bigdisk
	
sudo blkid /dev/sdb1
#/dev/sdb1: UUID="9dd30ea6-ddb9-4396-98fa-f5eeddface3d" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="primary" PARTUUID="4c81fe73-c946-41b4-9890-268dd2ca2683"
#UUID=9dd30ea6-ddb9-4396-98fa-f5eeddface3d /mnt/mydrive ext4 defaults 0 0

#add this to vfstab: /dev/disk/by-uuid/9dd30ea6-ddb9-4396-98fa-f5eeddface3d /mnt/mydrive ext4 defaults 0 0
sudo vi /etc/vfstab

```
## Jump Host cert

```
#Selfsigend cert generation
openssl genpkey -algorithm RSA -out key.pem
openssl req -new -key key.pem -out csr.pem -subj "/C=US/ST=CA/L=Dallas/O=OrfGelbrich/OU=R&D/CN=linuxjum.lab.local"
openssl x509 -req -in csr.pem -signkey key.pem -out cert.crt -days 365
```

## Jump host web server 

```
#Webserver
python3 http_server_auth.py --bind 127.0.0.1 --user vcf --password vcf123! --port 443 --directory /bigdisk --certfile ~/cert.crt --keyfile ~/key.pem

```

## Off line depo meta data link for the directoy structure

```
https://support.broadcom.com/group/ecx/productfiles?displayGroup=VMware%20Cloud%20Foundation%209&release=9.0.1.0&os=&servicePk=534266&language=EN&groupId=534924&viewGroup=true
```

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

#Selfsigned cert back on SDDC manager
https://vmw-confluence.broadcom.net/pages/viewpage.action?pageId=2233408098&spaceKey=~dzorgnotti&title=VCF%2B9%2B-%2BOffline%2BDepot%2Bwith%2BEnterprise%2BCA

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


## Nested ESX hosts (3) all look like this

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/take2/ESX_Hosts.png)

## All downloads are on the SDDC manager

![Version](https://github.com/ogelbric/VCF9-notes/blob/main/take2/SDDC_Down_Load_ALL.png)


### Take 2
