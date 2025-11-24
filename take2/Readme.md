# Off Line install VCF stack (take 2) 

## Cert for SDDC manager from jump box

```

Jump to SDDC Installer VM Console
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




### Take 2
