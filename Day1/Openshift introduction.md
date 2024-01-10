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
       e.) Openshift Controller - Once we deploy the application uski desired state ko current state se match karte rahna, Jese kisi application ke 4 containers he is desired state. and current running state like ek container down ho gaya. to to ye information api ko update karna controller ka kam he.
   
3. Worker nodes :

