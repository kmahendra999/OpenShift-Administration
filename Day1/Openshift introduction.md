Openshift Introduction :

It is a orchestration tools:

 - Centerlized management
 - failover
 - Reliability
 - Scalability
 - User Access Controll
 - Rollout and rollback features
 - Storage management for container
 - etc.

 #### Kubernetes : It is an opensource container orchestraion and management tools.
 
 #### Openshift : it is also orchestration and management tool for container technology
      It an Enterprise Opchestration tools based on kubernetes.
      It is a PAAS model. Because developer can put code and it create infra automaticlly.

## Openshift Archicture

1. Master nodes:
   
   i.) it is contoller for all worker nodes

   ii.) all administration tasks execure from master nodes.

   iii.) command handling / GUI / instruction all controlling master.

   iv.) Services : 

       a.) Openshift API - all features of openshift

       b.) Kube-apiServer - all features of k8 - **for all authentication and authorization for any instruction**

       c.) Openshift ETCD - Key Value Store Database. It containes all cluster api related infromation.

       d.) Openshift Scheduler - Container placement on the nodes. BAsed on the resources availability. It choose best fit node for the containers.

           Default Scheduling - system decide where pod will go

           Custom Scheduling -  we will decide where pods will go

       e.) Openshift Controller - Once we deploy the application uski desired state ko current state se match karte rahna,
           Jese kisi application ke 4 containers he is desired state. and current running state like ek container down ho gaya.
           to to ye information api ko update karna controller ka kam he.
           **So current state ko desired state se match karte rahna this is the job of controller.**
   
3. Worker nodes : So the Worker nodes ek machine he jiske upar we are deploying our application / containers.

   Compoents :

            a.) Container Runtime : jo container technology ko kernel me lekar aa raha he. Without runtime we can't use contanerization.

                                    Openshift me runtime is **cri-O**

                                    Now in daily , redhat ke har node par crio nam ki service dalni padegi.

                                    redhat ke 3.11 tak redhat was using containerd which is from docker community.

                                    But now it have 4.x se containerd is not available now and on each worker node redhat now using crio.

                                    crio ke sath ek service aur hoti he, **kubelet** : it is the agent of API server which understand api instructiuons and give to cri-o.

                                    koi bhi instruction pahle kubelet(api ka agent) ko jata he fir cri-o ke pas jata he.

            b.) **kubelet** : it is the agent of API server which understand api instructiuons and give to cri-o.

            c.) kube-proxy : it is used for networking

   INSTALLETION :

   OS : Linux needed (3.11 tak RHEL mendotry, after that REDHAT created Minimal core os, this is lightweight, it is only using only container technolofy footprint)

       REDHAT-COREOS ( CRI-O + Kublet + kubeproxy) for master and worker node both

   insalletion modes :

   upi: user provisioned infrastructor    -- --     all things done like machine create , installetion etc all.

    ipi: Intaller provsion infra : jo manual installetion nahi kar sakte vo bas installetion directoly script se karte he. it is complet automation
