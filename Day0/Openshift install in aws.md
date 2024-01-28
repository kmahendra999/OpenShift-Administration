Installing Openshift on AWS
<pre>
Prerequisites
aws configure command for configure aws cli on bastion node.

Disable Firewall or 
Refrence : https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html#configuring-firewall
Allowlist the URLs in your firewall.


Create Bastion node. so we can connect all openshift nodes via this.
Simple 2GB ram instance - aws ami.

##On Bastion Node :##
##################################
## So we can login to bastion using password again (optional commands 1 - 3 )
    1  vim /etc/ssh/sshd_config
	>>	PasswordAuthnetication yes
    2  sudo systemctl restart sshd
    3  sudo passwd ec2-user
	>>	Password = redhat
		
##Genrate keypair on bastion
    4  ssh-keygen

##Start the ssh-agent process as a background task:
    5  eval "$(ssh-agent -s)"
	
##Add your SSH private key to the ssh-agent:
    6  ssh-add .ssh/id_rsa

##Download Openshift Package 4.6.0
    7  wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/4.6.0/openshift-install-linux-4.6.0.tar.gz
    8  wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/4.6.0/openshift-client-linux-4.6.0.tar.gz
	
## or(7-8) for download latest package
##(7-8)wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz

## Extract the downloaded(9-10)
    9  tar -xvf openshift-install-linux-4.6.0.tar.gz
   10  tar -xvf openshift-client-linux-4.6.0.tar.gz
   
## or(9-10) for extract if we downloaded latest package
##(9-10)tar -xvf openshift-install-linux.tar.gz

## copy binaries to bin folders
   11  cp kubectl oc openshift-install /usr/local/bin/
   12  cp kubectl oc openshift-install /bin/

## AWS configure command and use a i am user with full privilleges
   13  aws configure

## making directory for keep the install-config.yaml file
   14  mkdir ocp

## Creating the isntall-config.yaml file for openshift configuretion
   15  vim ocp/install-config.yaml
apiVersion: v1
baseDomain: primemegamart.com 
controlPlane:   
  hyperthreading: Enabled 
  name: master
  platform:
    aws:
      zones:
      - us-east-1b
      rootVolume:
        iops: 4000
        size: 500
        type: io1 
      type: m6i.xlarge
  replicas: 1
compute: 
- hyperthreading: Enabled 
  name: worker
  platform:
    aws:
      rootVolume:
        iops: 2000
        size: 500
        type: io1 
      type: m6i.xlarge
      zones:
      - us-east-1b
  replicas: 2
metadata:
  name: merafirstcluster
networking:
  clusterNetwork:
  - cidr: 10.128.0.0/14
    hostPrefix: 23
  machineNetwork:
  - cidr: 10.0.0.0/16
  networkType: OpenShiftSDN
  serviceNetwork:
  - 172.30.0.0/16
platform:
  aws:
    region: us-east-1
sshKey: ssh-rsa xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/SkZQVUwQLDvJ/Jk1cYx2UKlRTDPyvSgZpgOb3+8I4rtfNhUQGqAq6xCLvEF4cVqrO9MMPGKktgp0zzV8zbg9L7kuMxzE2UFNBeLUB1HExnM2bCkLoN9GniKoDj0gteuHNdZwn1rEBmy4bntu8bN9lsNktBkFFstt5uJEfQ2phR/OpIV5Bzh8GHfVZVvTFs8oV7+3i8pA/mZhP5hfpMNdFLKhKMk2Fk7Y3dwnbHcm0TiSwLe4Np5DbtQOkSjevrwQaN/CF+6rDebvIh5cxL2inL+rgK6J7eTeR0CdrfXF9QkNN2yrms6/NXIVR83zHs1ty9ILi67m3F1+Tc2DSeHWfLNWn/cAx16EfAACf5grv5Z0xVK74B+RssmPPxChwjkkl3seLMiNMgC9BtorvMjJMqXCQCH1h/3UwR98PfRUoS36WrnePnTDP7FWnkqLxcM4LwsTDuExGRewqy/Nu3C/zwV2UhFji0omzfk= ec2-user@bastion 
pullSecret: '{"auths":{"cloud.openshift.com":{"auth":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxg0ZWY6UjJPREtPQ0FJMUNSRUJNRlNORTkyVEMyTkExQjVJREpIQU5OMk9ZQlpFMkcxMFBRVElENVQxSUhTQjM1UlNVMg==","email":"k.mahendra999@hotmail.com"},"quay.io":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfOTAwNzIzMTFjNDk0NGYzMmFjMTc0M2E0MDk2ZDg0ZWY6UjJPREtPQ0FJMUNSRUJNRlNORTkyVEMyTkExQjVJREpIQU5OMk9ZQlpFMkcxMFBRVElENVQxSUhTQjM1UlNVMg==","email":"k.mahendra999@hotmail.com"},"registry.connect.redhat.com":{"auth":"fHVoYy1wb29sLTZmMWJlZDMzLThhZmMtNDk3ZC04N2E5LWMyZWI2NzQ2YTViZTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhNRFF3WkdJeVlUSXdZakUwTW1VME9HSm1OekJpWldSa04yRXhObVUxWXlKOS5kNWJfdFh4MFRNY1hsVmFVS2V0YnJxV3NjMnE3RGd6MEFGWW1OVXMxRGNjYVA5ZWNUOTdraTQ0MHNyS3dwb014OHdHM0dzaEowbnMyRS1wZm8tYVRFd2FnU2JnRmVuSFo0UE9scWJ6c1ZRekpxUHdUWFdJb1FqRGVYRHhZbkU2YnQ2UEx2WGNhUnZTRUw3d04tUmVLdEFmQ0EwaEc0dzJvdEtoVXNSWjZpd0N6cGhWYWpJRGFqQmhrY0ZYSFZRN3hHZl9vaEl2X2xXSzNkOFE4cm55dmZWSlh4LUlPUTFJc1hUNjVvMjZpMG1pdVUzNlA4RUZVcS1HVnNSM3RmRXpUMXVQM0h0S1ljendWYnZ5R3IwbVhfNzh1amZRaWRyWncwaVFTbjFNSF9Zc3dBUzE5YjY1RXFwVTU4a0hCaGFlMnduVk05MEJqQmNDdWlVaEJ6MmlsMVZTQzdUWWRIQTZlaUhQY04zYl9pR19CUGk3S2x2Zk9hbkt4R0ZqWnJYc0UyU3o3ZFZOUWh2S3lHZXFhREktd0VrTXBnQjNCWTdKWXJmYWdTX21GVHJBcUZyM1N6clQxclFRcnRhQnJpbU9FYUpfYkhnLS1kY2hEX0NXOXJXOUExSTNkVW9TVWNKbkQzZDVVeFJWS0VrM2V5RFhOSjVKb0d6MlM0ajhXZnMtRnV0VUVweW91WmxiOU9mWnNNbnV2RTlSZjJGQjRwcTIyTkllOXFMWnpjLVVSS3BZVUFLUDYwNmVfc05VaGFtU1NtV2NDa19kdEZabGc5THJNb3h1U09TT3AyVGhiNzR6c2U4UUZNSloyRy1zV0Vnb0w2ZVRNNF9GNWIwVExDREd6M3pMSGVHR3JjVlhNYU1JQzZmUnVBdjNTcGpzNkVJWVNtdXNqbTRaZzBMWQ==","email":"k.mahendra999@hotmail.com"},"registry.redhat.io":{"auth":"fHVoYy1wb29sLTZmMWJlZDMzLThhZmMtNDk3ZC04N2E5LWMyZWI2NzQ2YTViZTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhNRFF3WkdJeVlUSXdZakUwTW1VME9HSm1OekJpWldSa04yRXhObVUxWXlKOS5kNWJfdFh4MFRNY1hsVmFVS2V0YnJxV3NjMnE3RGd6MEFGWW1OVXMxRGNjYVA5ZWNUOTdraTQ0MHNyS3dwb014OHdHM0dzaEowbnMyRS1wZm8tYVRFd2FnU2JnRmVuSFo0UE9scWJ6c1ZRekpxUHdUWFdJb1FqRGVYRHhZbkU2YnQ2UEx2WGNhUnZTRUw3d04tUmVLdEFmQ0EwaEc0dzJvdEtoVXNSWjZpd0N6cGhWYWpJRGFqQmhrY0ZYSFZRN3hHZl9vaEl2X2xXSzNkOFE4cm55dmZWSlh4LUlPUTFJc1hUNjVvMjZpMG1pdVUzNlA4RUZVcS1HVnNSM3RmRXpUMXVQM0h0S1ljendWYnZ5R3IwbVhfNzh1amZRaWRyWncwaVFTbjFNSF9Zc3dBUzE5YjY1RXFwVTU4a0hCaGFlMnduVk05MEJqQmNDdWlVaEJ6MmlsMVZTQzdUWWRIQTZlaUhQY04zYl9pR19CUGk3S2x2Zk9hbkt4R0ZqWnJYc0UyU3o3ZFZOUWh2S3lHZXFhREktd0VrTXBnQjNCWTdKWXJmYWdTX21GVHJBcUZyM1N6clQxclFRcnRhQnJpbU9FYUpfYkhnLS1kY2hEX0NXOXJXOUExSTNkVW9TVWNKbkQzZDVVeFJWS0VrM2V5RFhOSjVKb0d6MlM0ajhXZnMtRnV0VUVweW91WmxiOU9mWnNNbnV2RTlSZjJGQjRwcTIyTkllOXFMWnpjLVVSS3BZVUFLUDYwNmVfc05VaGFtU1NtV2NDa19kdEZabGc5THJNb3h1U09TT3AyVGhiNzR6c2U4UUZNSloyRy1zV0Vnb0w2ZVRNNF9GNWIwVExDREd6M3pMSGVHR3JjVlhNYU1JQzZmUnVBdjNTcGpzNkVJWVNtdXNqbTRaZzBMWQ==","email":"k.mahendra999@hotmail.com"}}}'

### For install openshift 
   16  openshift-install update cluster --log-level=info --dir=ocp/
</pre>
