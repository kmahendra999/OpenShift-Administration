Refrence :
https://docs.openshift.com/container-platform/4.10/installing/installing_aws/installing-aws-default.html


launch bastion node

os any ex aws

2gbram

key create

ssh allowed in security group

create instance

create i am user
go to iam >> 
user add >> add user
user name = ocp
enable accesskey programmatic access
>>netxt permission >>
attach existing polity >>administrator access

done
.csv download or access key id and secretn key


Route53
create hosted zone
add domain name
public zone
add/save

add 4 nameservers to domain in domain control panel.



connect bation from our local windows system


/etc/ssh/sshd_config

password authneticaion yes
restart sshd
passwd ec2-user
redhat

on bastion

hostnamectl set-hostname bation
bash



enable Quay.io registery access in your system


bastion node pe ek ssh-key build karo

ssh-keygen
eval "$(ssh-agent -s)"
ssh-add .ssh/id_rsa

need 500mb space for download files


go to openshift cluster manage :
https://console.redhat.com/openshift
Create cluster
choose AWS (x86_64) - Full stack automation and pre-existing infrastructure

Full control

Download installer copy link

https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/stable/openshift-install-linux.tar.gz

https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/
folder 4.6.0

openshift-install-linux-4.6.0.tar.gz
openshift-client-linux-4.6.0.tar.gz

>>>wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/4.6.0/openshift-install-linux-4.6.0.tar.gz
>>>wget https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/4.6.0/openshift-client-linux-4.6.0.tar.gz


tar -xvf openshift-install-linux-4.6.0.tar.gz
tar -xvf openshift-client-linux-4.6.0.tar.gz

tin binary aayegi extract hokar
if normal user se kam kar rahe he to
 mv kubectl oc openshift-isntall /usr/local/bin

else root se kam kar rahe he to /bin me copy karni he

openshift-install command me 3 masaster and 2 worker node pahle se hard coded he


in bastion >>
>>>aws configure
enter access key of iam user
enter secret key of iam user

openshift-install create cluster if abhi chala di to defailt master and worker nodes ke sath infra ban jayega

so creating customize installetion
mkdir ocp

Refrence : https://docs.openshift.com/container-platform/4.10/installing/installing_aws/installing-aws-customizations.html
Sample customized install-config.yaml file for AWS
get file from above url


vim ocp/install-config.yaml
-------------------------
apiVersion: v1
baseDomain: jodomainkadnssetupkiyatha.com 
credentialsMode: Mint 
controlPlane:   
  hyperthreading: Enabled 
  name: master
  platform:
    aws:
      zones:
      - us-east-1a
      - us-east-1b
      rootVolume:
        iops: 4000
        size: 500
        type: io1 
      type: m6i.xlarge
  replicas: 3
compute: 
- hyperthreading: Enabled 
  name: worker
  platform:
    aws:
      rootVolume:
        iops: 2000
        size: 500
        type: io1 
      type: c5.4xlarge
      zones:
      - us-east-2c
  replicas: 3
metadata:
  name: test-cluster 
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
sshKey: ssh-ed25519 AAAA... 
pullSecret: '{"auths": ...}' 
-------------------------



cat .ssh/id_rsa.pub ko second last line me dalna he
pullSecret >> redhat console se lana he copy karke

{"auths":{"cloud.openshift.com":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfOTAwNzIzMTFjNDk0NGYzMmFjMTc0M2E0MDk2ZDg0ZWY6UjJPREtPQ0FJMUNSRUJNRlNORTkyVEMyTkExQjVJREpIQU5OMk9ZQlpFMkcxMFBRVElENVQxSUhTQjM1UlNVMg==","email":"k.mahendra999@hotmail.com"},"quay.io":{"auth":"b3BlbnNoaWZ0LXJlbGVhc2UtZGV2K29jbV9hY2Nlc3NfOTAwNzIzMTFjNDk0NGYzMmFjMTc0M2E0MDk2ZDg0ZWY6UjJPREtPQ0FJMUNSRUJNRlNORTkyVEMyTkExQjVJREpIQU5OMk9ZQlpFMkcxMFBRVElENVQxSUhTQjM1UlNVMg==","email":"k.mahendra999@hotmail.com"},"registry.connect.redhat.com":{"auth":"fHVoYy1wb29sLTZmMWJlZDMzLThhZmMtNDk3ZC04N2E5LWMyZWI2NzQ2YTViZTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhNRFF3WkdJeVlUSXdZakUwTW1VME9HSm1OekJpWldSa04yRXhObVUxWXlKOS5kNWJfdFh4MFRNY1hsVmFVS2V0YnJxV3NjMnE3RGd6MEFGWW1OVXMxRGNjYVA5ZWNUOTdraTQ0MHNyS3dwb014OHdHM0dzaEowbnMyRS1wZm8tYVRFd2FnU2JnRmVuSFo0UE9scWJ6c1ZRekpxUHdUWFdJb1FqRGVYRHhZbkU2YnQ2UEx2WGNhUnZTRUw3d04tUmVLdEFmQ0EwaEc0dzJvdEtoVXNSWjZpd0N6cGhWYWpJRGFqQmhrY0ZYSFZRN3hHZl9vaEl2X2xXSzNkOFE4cm55dmZWSlh4LUlPUTFJc1hUNjVvMjZpMG1pdVUzNlA4RUZVcS1HVnNSM3RmRXpUMXVQM0h0S1ljendWYnZ5R3IwbVhfNzh1amZRaWRyWncwaVFTbjFNSF9Zc3dBUzE5YjY1RXFwVTU4a0hCaGFlMnduVk05MEJqQmNDdWlVaEJ6MmlsMVZTQzdUWWRIQTZlaUhQY04zYl9pR19CUGk3S2x2Zk9hbkt4R0ZqWnJYc0UyU3o3ZFZOUWh2S3lHZXFhREktd0VrTXBnQjNCWTdKWXJmYWdTX21GVHJBcUZyM1N6clQxclFRcnRhQnJpbU9FYUpfYkhnLS1kY2hEX0NXOXJXOUExSTNkVW9TVWNKbkQzZDVVeFJWS0VrM2V5RFhOSjVKb0d6MlM0ajhXZnMtRnV0VUVweW91WmxiOU9mWnNNbnV2RTlSZjJGQjRwcTIyTkllOXFMWnpjLVVSS3BZVUFLUDYwNmVfc05VaGFtU1NtV2NDa19kdEZabGc5THJNb3h1U09TT3AyVGhiNzR6c2U4UUZNSloyRy1zV0Vnb0w2ZVRNNF9GNWIwVExDREd6M3pMSGVHR3JjVlhNYU1JQzZmUnVBdjNTcGpzNkVJWVNtdXNqbTRaZzBMWQ==","email":"k.mahendra999@hotmail.com"},"registry.redhat.io":{"auth":"fHVoYy1wb29sLTZmMWJlZDMzLThhZmMtNDk3ZC04N2E5LWMyZWI2NzQ2YTViZTpleUpoYkdjaU9pSlNVelV4TWlKOS5leUp6ZFdJaU9pSXhNRFF3WkdJeVlUSXdZakUwTW1VME9HSm1OekJpWldSa04yRXhObVUxWXlKOS5kNWJfdFh4MFRNY1hsVmFVS2V0YnJxV3NjMnE3RGd6MEFGWW1OVXMxRGNjYVA5ZWNUOTdraTQ0MHNyS3dwb014OHdHM0dzaEowbnMyRS1wZm8tYVRFd2FnU2JnRmVuSFo0UE9scWJ6c1ZRekpxUHdUWFdJb1FqRGVYRHhZbkU2YnQ2UEx2WGNhUnZTRUw3d04tUmVLdEFmQ0EwaEc0dzJvdEtoVXNSWjZpd0N6cGhWYWpJRGFqQmhrY0ZYSFZRN3hHZl9vaEl2X2xXSzNkOFE4cm55dmZWSlh4LUlPUTFJc1hUNjVvMjZpMG1pdVUzNlA4RUZVcS1HVnNSM3RmRXpUMXVQM0h0S1ljendWYnZ5R3IwbVhfNzh1amZRaWRyWncwaVFTbjFNSF9Zc3dBUzE5YjY1RXFwVTU4a0hCaGFlMnduVk05MEJqQmNDdWlVaEJ6MmlsMVZTQzdUWWRIQTZlaUhQY04zYl9pR19CUGk3S2x2Zk9hbkt4R0ZqWnJYc0UyU3o3ZFZOUWh2S3lHZXFhREktd0VrTXBnQjNCWTdKWXJmYWdTX21GVHJBcUZyM1N6clQxclFRcnRhQnJpbU9FYUpfYkhnLS1kY2hEX0NXOXJXOUExSTNkVW9TVWNKbkQzZDVVeFJWS0VrM2V5RFhOSjVKb0d6MlM0ajhXZnMtRnV0VUVweW91WmxiOU9mWnNNbnV2RTlSZjJGQjRwcTIyTkllOXFMWnpjLVVSS3BZVUFLUDYwNmVfc05VaGFtU1NtV2NDa19kdEZabGc5THJNb3h1U09TT3AyVGhiNzR6c2U4UUZNSloyRy1zV0Vnb0w2ZVRNNF9GNWIwVExDREd6M3pMSGVHR3JjVlhNYU1JQzZmUnVBdjNTcGpzNkVJWVNtdXNqbTRaZzBMWQ==","email":"k.mahendra999@hotmail.com"}}}


openshift install create cluster --dir=ocp/




ipi method




#IPI=fullstack





register domainname.com
example.com
in domain control panel


