OpenShift CRC Installation:

Commands:

12GB/8VCPU/80HDD
<pre>
[ocp@localhost ~]$ cat /etc/yum.repos.d/dvd.repo 
[Baseos]
baseurl=file:///mnt/BaseOS
enabled=1
gpgcheck=0

[ApStream]
baseurl=file:///mnt/AppStream
enabled=1
gpgcheck=0


$ wget https://developers.redhat.com/content...
$ tar xvf crc-linux-amd64.tar.xz
$ sudo cd crc-linux-amd64
$ cp crc /bin
$ crc version 
$ crc setup




$ crc start             
enter pull secret

for secret
https://console.redhat.com/openshift/...


$ crc oc-env
export PATH="/home/vikas/.crc/bin/oc:$PATH"
Run this command to configure your shell:
eval $(crc oc-env)
$ vim ~/.bashrc
export PATH="~/.crc/bin:$PATH"
eval $(crc oc-env)
$source ~/.bashrc  
$oc cluster-info
$ oc config view
$ oc get all
$ oc get clusteroperators
$ oc get nodes
$[vikas@crcsetup ~]$ crc console --credentials
To login as a regular user, run 'oc login -u developer -p developer https://api.crc.testing:6443'.
To login as an admin, run 'oc login -u kubeadmin -p 9uSkR-qdLJN-tf5qK-fjmAr https://api.crc.testing:6443'
[vikas@crcsetup ~]$ 
</pre>

Video on youtube : https://youtu.be/MhKMDLZEV-o?si=kA59_nJjO8bV0Lxu
